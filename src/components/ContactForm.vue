<script setup lang="ts">
import { ref } from 'vue';

const FORM_ID  = import.meta.env.VITE_SMARTFORM_FORM_ID as string;
const ENDPOINT = 'https://api.usesmartform.com/api/v1/f';
const status   = ref<string>('');

async function onSubmit(e: Event) {
  e.preventDefault();
  status.value = 'Sending…';
  const data = Object.fromEntries(new FormData(e.currentTarget as HTMLFormElement));
  try {
    const r = await fetch(`${ENDPOINT}/${FORM_ID}`, {
      method:  'POST',
      headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
      body:    JSON.stringify(data),
    });
    const body = await r.json();
    status.value = `Sent! submission_id=${body.submission_id} intent=${body.intent}`;
  } catch (err: any) {
    status.value = `Error: ${err.message}`;
  }
}
</script>

<template>
  <form @submit="onSubmit" style="display: grid; gap: 12px;">
    <p v-if="!FORM_ID">Set <code>VITE_SMARTFORM_FORM_ID</code> in <code>.env</code> first.</p>
    <template v-else>
      <input name="name"  placeholder="Name"  required />
      <input name="email" type="email" placeholder="Email" required />
      <textarea name="message" placeholder="Message" required style="min-height: 100px;"></textarea>
      <input type="text" name="_gotcha" tabindex="-1" autocomplete="off"
             style="position: absolute; left: -9999px;" aria-hidden="true" />
      <button type="submit" style="background: #7c3aed; color: #fff; border: 0; padding: 8px 10px;">Send</button>
      <p>{{ status }}</p>
    </template>
  </form>
</template>
