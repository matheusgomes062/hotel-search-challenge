<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import { useHotelsStore } from '@/stores/hotels'
import type { HotelSearchParams } from '@/types'
import InputComponent from './ui/InputComponent.vue'

const hotelsStore = useHotelsStore()

const destination = ref<string>('')
const checkInDate = ref<string>('')
const checkOutDate = ref<string>('')
const roomCount = ref<number>(1)
const guestCount = ref<number>(1)

const today = new Date().toISOString().split('T')[0]

const minCheckInDate = computed(() => today)

const minCheckOutDate = computed(() => {
  return checkInDate.value
    ? new Date(new Date(checkInDate.value).getTime() + 86400000).toISOString().split('T')[0]
    : today
})

watch(checkInDate, (newCheckInDate) => {
  if (checkOutDate.value && new Date(checkOutDate.value) <= new Date(newCheckInDate)) {
    checkOutDate.value = new Date(new Date(newCheckInDate).getTime() + 86400000)
      .toISOString()
      .split('T')[0]
  }
})

const handleSubmit = async () => {
  const searchParams: HotelSearchParams = {
    city: destination.value,
    checkIn: checkInDate.value,
    checkOut: checkOutDate.value,
    roomCount: roomCount.value,
    guestCount: guestCount.value
  }

  await hotelsStore.fetchHotels(searchParams)
}
</script>

<template>
  <div class="w-full">
    <form @submit.prevent="handleSubmit" class="flex flex-col flex-wrap gap-4">
      <div
        class="form-group w-full rounded-xl md:rounded-full bg-white items-center px-5 md:px-10 py-2 md:py-5 border"
      >
        <InputComponent
          id="destination"
          v-model="destination"
          data-test="destination-input"
          label="Destination:"
          placeholder="Enter destination"
          input-class="text-sm h-8 w-full border-gray-300 outline-none"
        />
      </div>

      <div
        class="form-group w-full flex flex-col md:flex-row gap-4 md:gap-0 rounded-xl md:rounded-full bg-white items-center px-5 md:px-10 py-2 md:py-5 border justify-between"
      >
        <div class="w-full md:w-auto md:border-r md:pr-8 border-gray-300 ml-4">
          <label for="checkIn" class="block text-sm font-medium text-gray-700"
            >Check-In Date:</label
          >
          <input
            type="date"
            id="checkIn"
            data-test="checkIn-input"
            v-model="checkInDate"
            :min="minCheckInDate"
            class="text-sm h-8 w-full border-gray-300 outline-none"
          />
        </div>

        <div class="w-full md:w-auto md:border-r md:pr-8 border-gray-300 ml-4">
          <label for="checkOut" class="block text-sm font-medium text-gray-700"
            >Check-Out Date:</label
          >
          <input
            type="date"
            id="checkOut"
            data-test="checkOut-input"
            v-model="checkOutDate"
            :min="minCheckOutDate"
            class="text-sm h-8 w-full border-gray-300 outline-none"
          />
        </div>

        <div class="w-full md:w-auto grid grid-cols-2 gap-4">
          <div class="border-r md:pr-8 border-gray-300 ml-4">
            <InputComponent
              id="roomCount"
              v-model="roomCount"
              data-test="roomCount-input"
              label="Rooms:"
              type="number"
              :min="1"
              input-class="h-8 text-sm w-full outline-none border-none"
            />
          </div>
          <div class="md:border-r md:pr-8 border-gray-300 ml-4">
            <InputComponent
              id="guestCount"
              v-model="guestCount"
              data-test="guestCount-input"
              label="Guests:"
              type="number"
              :min="1"
              input-class="h-8 text-sm w-full outline-none border-none"
            />
          </div>
        </div>

        <button
          type="submit"
          data-test="search-button"
          class="w-full md:w-auto ml-4 px-4 h-10 text-white font-semibold rounded-full shadow bg-violet-600 hover:bg-violet-700"
        >
          Search
        </button>
      </div>
    </form>
  </div>
</template>

<style scoped>
/* Add any additional custom styles if needed */
</style>
