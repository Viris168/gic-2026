<script setup lang="ts">
import { computed, ref, watch } from "vue";
import EventList from "./components/EventList.vue";
import { initialEvents } from "./data/events";
import type { CampusEvent } from "./types";

// A reactive copy of the hard-coded events
const events = ref<CampusEvent[]>(initialEvents.map(e => ({ ...e })));

// TODO 1: a ref `myBookings` holding the ids of the events I booked (one entry per seat)
// TODO 2: a computed `bookingCount`

const myBookings = ref<number[]>([]);
const bookingCount = computed(() => myBookings.value.length);


// TODO 3: onBook(id): find the event; if it still has a seat,
//         increase its `booked` and add the id to myBookings
function onBook(id: number) {
  const event = events.value.find(e => e.id === id);
  if(!event) return;
  if(event.booked >= event.capacity){
    console.warn(`Event ${id} is full`)
    return
  }
  event.booked += 1;
  myBookings.value.push(id);
}

// TODO 4 (bonus): keep my bookings after a page reload
//   - watch myBookings and save it to localStorage (JSON.stringify)
//   - read it back when the app starts, and add those seats to `booked`

const STORAGE_KEY = 'myBookings'

try {
  const saved = localStorage.getItem(STORAGE_KEY);
  if (saved) {
    const parsed = JSON.parse(saved);
    if (Array.isArray(parsed)) {
      myBookings.value = parsed.filter((id): id is number => typeof id === "number");
    }
  }
} catch (e) {
  console.log("Could not restore bookings", e);
  myBookings.value = [];
}

// Re-apply the restored seats to each event's `booked` count.
for (const id of myBookings.value) {
  const event = events.value.find(e => e.id === id);
  if (event) event.booked = Math.min(event.booked + 1, event.capacity);
}

// Persist after every change (the watch is created after restoring,
// so the initial empty array never overwrites saved bookings).
watch(
  myBookings,
  value => {
    try {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(value));
    } catch (e) {
      console.log("Could not save bookings", e);
    }
  },
  { deep: true },
);

</script>

<template>
  <main>
    <header>
      <h1>Campus Events</h1>
      <!-- TODO 5: show the real count ("1 seat" / "N seats") -->
      <p class="mine">
        You have booked {{ bookingCount }} {{ bookingCount === 1 ? "seat" : "seats" }}.
      </p>
    </header>
    <EventList :events="events" @book="onBook" />
  </main>
</template>

<style scoped>
header { margin-bottom: 16px; }
.mine { color: #6b7280; margin: 0; }
</style>
