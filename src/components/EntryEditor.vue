<script lang="ts" setup>
import EmojiField from "@/components/EmojiField.vue";
import ArrowCircleRight from "@/assets/icons/arrow-circle-right.svg";

import { ref, computed, onMounted, inject } from "vue";
import { userInjectionKey } from "@/injectionKeys";

import type Emoji from "@/types/Emoji";
import type Entry from "@/types/Entry";

// Data
const body = ref("");
const emoji = ref<Emoji | null>(null);
const charCount = computed(() => body.value.length);
const maxCharCount = 280;
const user = inject(userInjectionKey);

// Events
const emit = defineEmits<{
  (e: "@create", entry: Entry): void;
}>();

// Template refs
const textarea = ref<HTMLTextAreaElement | null>(null);
onMounted(() => textarea.value?.focus());

// Methods
const handleTextInput = (event: Event) => {
  const textarea = event.target as HTMLTextAreaElement;
  if (textarea.value.length <= maxCharCount) {
    body.value = textarea.value;
  } else {
    body.value = textarea.value = textarea.value.substring(0, maxCharCount);
  }
};

const handleSubmit = () => {
  emit("@create", {
    body: body.value,
    emoji: emoji.value,
    createdAt: new Date(),
    userId: 1,
    id: Math.random(),
  });

  body.value = "";
  emoji.value = null;
};
</script>

<template>
  <form class="entry-form" @submit.prevent="handleSubmit">
    <textarea
      ref="textarea"
      :value="body"
      :placeholder="`New Journal Entry for ${user?.username}`"
      @keyup="handleTextInput"
    />
    <EmojiField v-model="emoji" />
    <div class="entry-form-footer">
      <span>{{ charCount }} / {{ maxCharCount }}</span>
      <button>Remember <ArrowCircleRight width="20" /></button>
    </div>
  </form>
</template>
