# Week 3 Practice: Answers

### 1. In `EventList`, what goes wrong if you use the array index as `:key` and the list gets filtered while a card has local state (for example an open details panel)?
If array indexes are used as `:key`, Vue identifies and matches DOM nodes by their positional index rather than their unique entity identity. When the list is filtered, the element at index `0` changes, but Vue reuses the existing component instance along with its internal state (like an open details toggle). As a result, the open panel stays on whatever card shifts into that position instead of following the original event.

### 2. Why is `visible` a computed and not a function called from the template? What is the difference when you type in the search box?
A `computed` property caches its result based on its reactive dependencies (`props.events`, `search`, and `category`) and only recalculates when one of those dependencies changes. In contrast, calling a method from the template causes that function to re-run on every single render cycle, even when unrelated component state changes. Using `computed` ensures array filtering runs only when filter criteria actually change, keeping typing responsive.

### 3. `EventCard` emits `book` instead of changing `event.booked`. Give two reasons.
First, props in Vue adhere to a strict one-way data flow and are read-only; directly mutating `event.booked` violates this principle and triggers a Vue runtime warning. Second, `App.vue` is the owner of the events array and centralizes booking logic (such as checking remaining capacity and tracking user bookings in `myBookings`), so state mutations should be handled by the parent component.

### 4. You write `const { booked } = reactive(event); booked++;`. The page does not change. Why?
Destructuring a reactive object unwraps primitive values (such as numbers) into standalone local variables by value rather than by reference, thereby breaking Vue's reactivity tracking. Modifying the local variable `booked` increments only that isolated primitive and leaves both the original object and Vue's reactivity system unaware of any change.

### 5. Where does `myBookings` live, and why not in `EventCard` or `EventList`?
`myBookings` lives in `App.vue` because it represents shared application state needed by multiple child components, including the header counter and the event list. If stored locally inside `EventCard` or `EventList`, sibling components would not have access to it, violating the single source of truth principle and requiring complex prop drilling or event passing.
