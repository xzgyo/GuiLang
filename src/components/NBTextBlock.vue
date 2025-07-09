<template>
  <div class="card h-100 d-flex flex-column p-2">
    <div class="card-header py-2 px-3">
      {{ title }}
    </div>
    <div class="card-body p-0 flex-grow-1">
      <textarea
        class="form-control h-100 w-100 border-0"
        :readonly="readonly"
        :placeholder="placeholder"
        :value="modelValue"
        @input="$emit('update:modelValue', ($event.target as HTMLTextAreaElement).value)"
        style="resize: none; overflow-y: auto; font-family: monospace; padding: 1rem;"
      ></textarea>
    </div>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  title: string
  modelValue: string
  readonly?: boolean
  placeholder?: string
}>()

defineEmits(['update:modelValue'])
</script>
