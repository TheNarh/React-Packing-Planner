# 🌴 Far Away — React Packing Planner

An interactive trip packing list application built with React, featuring real-time item management, multi-criteria sorting, packed status tracking and live packing progress statistics.

> Built to demonstrate core React fundamentals at a production level: controlled components, inverse data flow, derived state, and clean component architecture.

---

## Live Demo

[https://react-packing-planner.vercel.app/]

---

## Preview

> <img width="1911" height="921" alt="StartPage" src="https://github.com/user-attachments/assets/5b8d36cd-725b-4f4f-a99b-2d640cc15811" />
<img width="1914" height="901" alt="PageInAction" src="https://github.com/user-attachments/assets/f06897ef-64ae-426e-a2d4-5c2f268fe54a" />
<img width="1821" height="920" alt="PageInAction1" src="https://github.com/user-attachments/assets/e5b2bfe6-43fd-4e31-8961-002f7a6f3631" />




---

## What This App Does

- Add items to your packing list with a **quantity selector** (1–20) and description
- **Check off items** as you pack them — they strike through automatically
- **Sort your list** by input order, alphabetical description, or packed status
- See **live packing statistics** — how many items packed, how many remain, and your percentage progress
- **Clear the entire list** with a confirmation guard so you never delete by accident
- Fully **controlled form** — resets cleanly after every submission

---

## Architecture — Lifting State Up

The most important architectural decision in this app is where state lives.

All item data lives in `App.jsx` — the single source of truth. Every child component that needs to read or modify the list receives what it needs through **props and callback functions.**

```
App.jsx  ←  owns all state
  ├── Form.jsx        ← adds items     (calls onAddItems)
  ├── PackingList.jsx ← displays items (calls onDeleteItem, onToggleItem, onClearList)
  │     └── Item.jsx  ← single item    (calls onDeleteItem, onToggleItem)
  └── Stats.jsx       ← reads items    (derived statistics only)
```

This is the **inverse data flow** pattern — data flows down as props, events flow up as callbacks. No prop drilling hacks. No shared mutable state. Clean and predictable.

---

## Key Technical Decisions

### 1. Single Source of Truth

All four operations on the list — add, delete, toggle, clear — are defined in `App.jsx` and passed down as props. No child component owns or mutates the list directly:

```javascript
const [items, setItems] = useState([]);

function handleAddItems(item) {
  setItems((items) => [...items, item]);
}

function handleDeleteItem(id) {
  setItems((items) => items.filter((item) => item.id !== id));
}

function handleToggleItem(id) {
  setItems((items) =>
    items.map((item) =>
      item.id === id ? { ...item, packed: !item.packed } : item
    )
  );
}
```

Every state update uses the **functional form** of `setItems` — always reading the latest state rather than a potentially stale closure value.

### 2. Controlled Form With Guard Clause

The `Form` component is fully controlled — every input is bound to state and resets cleanly after submission. A guard clause prevents empty items from ever entering the list:

```javascript
function handleSubmit(e) {
  e.preventDefault();

  if (!description) return;  // guard clause — no empty items

  const newItem = { description, quantity, packed: false, id: Date.now() };
  onAddItems(newItem);

  setDescription("");
  setQuantity(1);
}
```

Using `Date.now()` as the item ID is a lightweight, dependency-free way to guarantee uniqueness across additions.

### 3. Quantity Selector — Generated Dynamically

The quantity dropdown (1–20) is generated programmatically — no hardcoded option tags:

```javascript
Array.from({ length: 20 }, (_, i) => i + 1).map((num) => (
  <option value={num} key={num}>{num}</option>
))
```

Clean, scalable, and easy to adjust the range without touching markup.

### 4. Multi-Criteria Sorting With Derived State

Sorting lives entirely inside `PackingList` as local state — it doesn't affect the actual data in `App`, only how it's displayed. Three sort strategies, each with a different comparison:

```javascript
if (sortBy === "input")       sortedItems = items;
if (sortBy === "description") sortedItems = items
  .slice()
  .sort((a, b) => a.description.localeCompare(b.description));
if (sortBy === "packed")      sortedItems = items
  .slice()
  .sort((a, b) => Number(a.packed) - Number(b.packed));
```

Note the deliberate use of `.slice()` before `.sort()` — this creates a copy of the array so the original state in `App` is never mutated directly.

### 5. Derived State in Stats

`Stats` receives the raw items array and computes everything itself — no pre-calculated values passed as props. This keeps the parent lean and the statistics always in sync:

```javascript
const numItems = items.length;
const numPacked = items.filter((item) => item.packed).length;
const percentage = Math.round((numPacked / numItems) * 100);
```

When everything is packed, the message changes entirely:

```javascript
percentage === 100
  ? "You got everything! Ready to go 🛫"
  : `👜 You have ${numItems} items and already packed ${numPacked} (${percentage}%)`
```

### 6. Confirmation Guard on Clear

Destructive actions need friction. The clear list function uses a native confirm dialog to prevent accidental data loss:

```javascript
function handleClearList() {
  const confirmed = window.confirm("Are you sure you want to delete all items?");
  if (confirmed) setItems([]);
}
```

### 7. Immutable Toggle

Toggling packed status uses `.map()` to return a brand new array — the original is never touched:

```javascript
items.map((item) =>
  item.id === id ? { ...item, packed: !item.packed } : item
)
```

The spread operator `{ ...item }` creates a new object for the matched item — React detects the reference change and re-renders only what needs to update.

---

## Component Structure

```
App.jsx           ← state owner, event handlers, orchestration
├── Logo.jsx      ← app title and branding
├── Form.jsx      ← controlled form, item creation, input validation
├── PackingList   ← list display, sorting logic, clear action
│   └── Item.jsx  ← single list item, checkbox, delete button
└── Stats.jsx     ← derived statistics, progress feedback
```

Every component has **one job.** No component does more than it needs to.

---

## Project Structure

```
react-packing-planner/
├── public/
├── src/
│   ├── components/
│   │   ├── Form.jsx          ← controlled form + item creation
│   │   ├── Item.jsx          ← single packing item
│   │   ├── Logo.jsx          ← app header
│   │   ├── PackingList.jsx   ← list + sorting + clear
│   │   └── Stats.jsx         ← live packing statistics
│   ├── App.jsx               ← root component + all state
│   └── index.js
├── package.json
└── README.md
```

---

## Tech Stack

| Technology | Purpose |
|---|---|
| React 18 | UI library |
| useState | Local and lifted state management |
| Controlled Components | Form inputs and select elements |
| Derived State | Live statistics calculation |
| CSS3 | Styling and layout |

---

## Getting Started

### Prerequisites
- Node.js v16+
- npm or yarn

### Installation

```bash
# Clone the repo
git clone https://github.com/TheNarh/React-Packing-Planner.git

# Navigate into the project
cd React-Packing-Planner

# Install dependencies
npm install

# Start the app
npm start
```

Then open [http://localhost:3000](http://localhost:3000)

---

## What I Learned Building This

- How to **lift state up** to a common ancestor so multiple components can share and modify the same data
- Why **inverse data flow** — passing callbacks down as props — keeps components decoupled and predictable
- How to use **derived state** to compute statistics without storing redundant data
- Why `.slice()` before `.sort()` matters — never mutate state arrays directly
- How **controlled components** give you full control over form inputs and make resets trivial
- The importance of **guard clauses** for input validation and destructive action confirmation

---

## Future Improvements

- [ ] Persist packing list to `localStorage` so items survive a page refresh
- [ ] Add trip categories — clothes, toiletries, documents, electronics
- [ ] Drag and drop to reorder items manually
- [ ] Edit existing items inline
- [ ] Export packing list as PDF or share via link

---

## Author

**Ludwig Sackey Narh** — Software Engineer

- 🌍 GitHub: [@TheNarh](https://github.com/TheNarh)
- 💼 LinkedIn: [https://www.linkedin.com/in/ludwig-sackey-narh-670078106/](#)
- 📧 thenarh17@gmail.com

---

## 📄 License

MIT — feel free to use this as a reference or starting point for your own list management app.
