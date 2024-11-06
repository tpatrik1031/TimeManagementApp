<template>
  <div class="absolute w-full h-full bg-black bg-opacity-50 z-10 flex items-center justify-center" v-if="show">
    <div class="bg-gradient-to-r from-gray-700 to-gray-500 p-6 rounded-xl shadow-lg w-11/12 md:w-1/3 mx-auto border border-gray-400">
      <p class="text-lg font-semibold mb-6 text-white">Biztosan törölni szeretné az összes feladatot?</p>
      <div class="flex justify-end space-x-4">
        <button @click="emitConfirm" class="bg-red-600 text-white px-4 py-2 rounded-lg hover:bg-red-800 transition">
          Igen
        </button>
        <button @click="emitCancel" class="bg-gray-400 text-white px-4 py-2 rounded-lg hover:bg-gray-600 transition">
          Nem
        </button>
      </div>
    </div>
  </div>
</template>
  
<script setup>
  const props = defineProps({
    show: {
      type: Boolean,
      default: false,
    },
  })
  
  const emit = defineEmits(['confirm', 'cancel'])
  const emitConfirm = () => emit('confirm')
  const emitCancel = () => emit('cancel')
</script>  