# Security System Bundle Builder - Full Project Source Code

All source files for the **Frontend Take-Home Bundle Builder** project are located in your Desktop workspace folder:
`c:\Users\9body\Desktop\Frontend Take-Home Bundle Builder`

---

## Folder & File Structure

```
c:\Users\9body\Desktop\Frontend Take-Home Bundle Builder\
├── README.md
├── package.json
├── package-lock.json
├── tsconfig.json
├── tsconfig.app.json
├── tsconfig.node.json
├── vite.config.ts
├── vitest.config.ts
├── .gitignore
├── index.html
├── public/
│   ├── favicon.svg
│   ├── icons.svg
│   └── images/
│       └── products/
│           ├── cam-pro-white.svg
│           ├── cam-pro-black.svg
│           ├── doorbell-cam.svg
│           ├── contact-sensor.svg
│           ├── solar-panel.svg
│           └── secure-plus.svg
└── src/
    ├── App.tsx
    ├── App.css
    ├── index.css
    ├── main.tsx
    ├── components/
    │   ├── AccordionStep.tsx
    │   ├── Builder.tsx
    │   ├── BuilderLayout.tsx
    │   ├── OrderSummary.tsx
    │   ├── ProductCard.tsx
    │   ├── ProductCard.test.tsx
    │   ├── QuantityStepper.tsx
    │   ├── ReviewCategoryGroup.tsx
    │   ├── ReviewItem.tsx
    │   ├── ReviewPanel.tsx
    │   └── VariantSelector.tsx
    ├── context/
    │   └── BundleContext.tsx
    ├── data/
    │   └── products.json
    ├── hooks/
    │   └── useLocalStorage.ts
    ├── types/
    │   └── product.ts
    └── utils/
        └── review.ts
```

---

## 1. Data (`src/data/products.json`)

```json
[
  {
    "id": "cam-pro",
    "category": "cameras",
    "step": 1,
    "title": "Outdoor Security Camera Pro",
    "description": "4K HDR video, color night vision.",
    "discountBadge": "Save 22%",
    "compareAtPrice": 199.99,
    "price": 155.99,
    "image": "/images/products/cam-pro-white.svg",
    "hasVariants": true,
    "variants": [
      { "id": "cam-pro-white", "name": "White", "image": "/images/products/cam-pro-white.svg" },
      { "id": "cam-pro-black", "name": "Black", "image": "/images/products/cam-pro-black.svg" }
    ]
  },
  {
    "id": "doorbell-cam",
    "category": "cameras",
    "step": 1,
    "title": "Video Doorbell",
    "description": "HD video, two-way talk, and motion alerts.",
    "price": 89.99,
    "image": "/images/products/doorbell-cam.svg",
    "hasVariants": false
  },
  {
    "id": "contact-sensor",
    "category": "sensors",
    "step": 2,
    "title": "Smart Contact Sensor",
    "description": "Instant open and close notifications for doors and windows.",
    "price": 24.99,
    "image": "/images/products/contact-sensor.svg",
    "hasVariants": false
  },
  {
    "id": "solar-panel",
    "category": "accessories",
    "step": 3,
    "title": "Solar Panel Charger",
    "description": "Continuous power for compatible outdoor cameras.",
    "price": 34.99,
    "image": "/images/products/solar-panel.svg",
    "hasVariants": false
  },
  {
    "id": "secure-plus",
    "category": "plans",
    "step": 4,
    "title": "Secure Plus Plan",
    "description": "30-day cloud history and advanced alert features.",
    "price": 9.99,
    "image": "/images/products/secure-plus.svg",
    "hasVariants": false
  }
]
```

---

## 2. Product Types (`src/types/product.ts`)

```typescript
export interface ProductVariant {
  id: string
  name: string
  image?: string
}

export interface Product {
  id: string
  category: string
  step: number
  title: string
  description?: string
  discountBadge?: string
  compareAtPrice?: number
  price: number
  hasVariants: boolean
  variants?: ProductVariant[]
  image?: string
}
```

---

## 3. Product Card Component (`src/components/ProductCard.tsx`)

```tsx
import { useBundle } from '../context/BundleContext'
import type { Product } from '../types/product'
import { QuantityStepper } from './QuantityStepper'

interface ProductCardFromDataProps {
  product: Product
}

interface ProductCardFromProps {
  id: string
  title: string
  description?: string
  price: number
  compareAtPrice?: number
  variants?: Product['variants']
  discountBadge?: string
  quantity?: number
  product?: never
}

type ProductCardProps = ProductCardFromDataProps | ProductCardFromProps

const hasProductData = (props: ProductCardProps): props is ProductCardFromDataProps =>
  props.product !== undefined

const formatPrice = (price: number): string =>
  new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
  }).format(price)

const getSwatchColor = (name: string): string => {
  const colors: Record<string, string> = {
    white: '#ffffff',
    black: '#111827',
    red: '#dc2626',
    blue: '#2563eb',
    gray: '#6b7280',
    grey: '#6b7280',
  }

  return colors[name.toLowerCase()] ?? '#e5e7eb'
}

export function ProductCard(props: ProductCardProps): React.ReactElement {
  const isDataBound = hasProductData(props)
  const product: Product = isDataBound
    ? props.product
    : {
        id: props.id,
        category: 'custom',
        step: 1,
        title: props.title,
        description: props.description,
        discountBadge: props.discountBadge,
        compareAtPrice: props.compareAtPrice,
        price: props.price,
        hasVariants: (props.variants?.length ?? 0) > 0,
        variants: props.variants,
      }
  const { selections, activeVariants, updateQuantity, setActiveVariant } = useBundle()
  const variants = product.variants ?? []
  const defaultVariantId = variants[0]?.id
  const activeVariantId = activeVariants[product.id] ?? defaultVariantId
  const currentId = product.hasVariants && activeVariantId ? activeVariantId : product.id
  const quantity = isDataBound
    ? (selections[currentId] ?? 0)
    : (props.quantity ?? selections[currentId] ?? 0)
  const productSelectionIds = product.hasVariants
    ? variants.map((variant) => variant.id)
    : [product.id]
  const isSelected = isDataBound
    ? productSelectionIds.some((id) => (selections[id] ?? 0) > 0)
    : quantity > 0

  const activeVariant = variants.find((v) => v.id === activeVariantId)
  const displayImage = activeVariant?.image ?? product.image

  return (
    <article
      className={`group relative overflow-hidden rounded-2xl border p-4 shadow-sm transition-all sm:p-5 ${
        isSelected ? 'border-blue-600 bg-blue-50/40 ring-1 ring-blue-600/20' : 'border-slate-200 bg-white hover:border-slate-300 hover:shadow-md'
      }`}
    >
      {product.discountBadge && (
        <span className="absolute left-4 top-4 z-10 rounded-full bg-indigo-600 px-2.5 py-1 text-xs font-bold text-white shadow-sm">
          {product.discountBadge}
        </span>
      )}

      <div className="relative mb-4 aspect-[16/9] overflow-hidden rounded-xl bg-slate-100">
        {displayImage ? (
          <img
            src={displayImage}
            alt={product.title}
            className="size-full object-cover object-center transition-transform duration-500 ease-out group-hover:scale-105"
            loading="lazy"
          />
        ) : (
          <div className="grid size-full place-items-center text-sm font-medium text-slate-400">
            Product image
          </div>
        )}
      </div>

      <h3 className="text-base font-bold text-gray-900 sm:text-lg">{product.title}</h3>
      {product.description && <p className="mt-1 text-sm leading-5 text-gray-500">{product.description}</p>}
      <a href="#learn-more" className="mt-2 inline-block text-sm font-semibold text-blue-600 hover:text-blue-700 hover:underline">
        Learn More
      </a>

      {product.hasVariants && variants.length > 0 && activeVariantId && (
        <div className="mt-4 flex items-center gap-2" aria-label="Available colors">
          {variants.map((variant) => {
            const isActive = variant.id === activeVariantId

            return (
              <button
                key={variant.id}
                type="button"
                aria-label={variant.name}
                aria-pressed={isActive}
                title={variant.name}
                onClick={() => setActiveVariant(product.id, variant.id)}
                className={`grid size-7 place-items-center rounded-full transition focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 ${
                  isActive ? 'ring-2 ring-blue-600 ring-offset-2' : 'hover:ring-2 hover:ring-slate-300 hover:ring-offset-2'
                }`}
              >
                <span
                  className="size-5 rounded-full border border-slate-300"
                  style={{ backgroundColor: getSwatchColor(variant.name) }}
                />
              </button>
            )
          })}
        </div>
      )}

      <div className="mt-5 flex flex-col-reverse gap-4 border-t border-slate-200 pt-4 sm:flex-row sm:items-end sm:justify-between">
        <QuantityStepper
          quantity={quantity}
          onQuantityChange={(nextQuantity) => updateQuantity(currentId, nextQuantity)}
          label={product.title}
        />
        <div className="sm:text-right">
          <span className="text-lg font-bold text-gray-900 sm:text-xl">{formatPrice(product.price)}</span>
          {product.compareAtPrice !== undefined && (
            <span className="ml-2 text-sm text-gray-400 line-through">
              {formatPrice(product.compareAtPrice)}
            </span>
          )}
        </div>
      </div>
    </article>
  )
}
```

---

## 4. Review Item Component (`src/components/ReviewItem.tsx`)

```tsx
import { useBundle } from '../context/BundleContext'
import type { ReviewLineItem } from '../utils/review'
import { formatCurrency } from '../utils/review'
import { QuantityStepper } from './QuantityStepper'

interface ReviewItemProps {
  item: ReviewLineItem
}

export function ReviewItem({ item }: ReviewItemProps): React.ReactElement {
  const { updateQuantity } = useBundle()
  const lineTotal = item.unitPrice * item.quantity

  return (
    <article className="flex items-center gap-3 py-4">
      <div className="relative grid size-12 shrink-0 overflow-hidden rounded-lg bg-slate-100 sm:size-14">
        {item.image ? (
          <img
            src={item.image}
            alt={item.name}
            className="size-full object-cover object-center"
            loading="lazy"
          />
        ) : (
          <div className="grid size-full place-items-center text-xs font-medium text-slate-400">
            Image
          </div>
        )}
      </div>
      <h3 className="min-w-0 flex-1 text-sm font-semibold leading-5 text-slate-900">{item.name}</h3>
      <div className="flex shrink-0 flex-col items-end gap-2">
        <p className="text-sm font-bold text-slate-950">{formatCurrency(lineTotal)}</p>
        <QuantityStepper
          quantity={item.quantity}
          onQuantityChange={(nextQuantity) => updateQuantity(item.selectionId, nextQuantity)}
          label={item.name}
        />
      </div>
    </article>
  )
}
```

---

## 5. Repository Readme (`README.md`)

```markdown
# Security System Bundle Builder

An interactive, responsive React + TypeScript e-commerce bundle builder application allowing clients and users to build a custom security protection system step-by-step with real-time pricing, dynamic product image variant previews, item count tracking, and persistent cart state.

## Quick Start & Run Instructions

```bash
# 1. Install dependencies
npm install

# 2. Start development server
npm run dev

# 3. Run unit tests
npm test

# 4. Build for production
npm run build
```
```
