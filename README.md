# SmartForm + Vite + Vue 3

Contact form for a Vite + Vue 3 app, posting JSON to SmartForm AI.

## Setup

1. Get a form ID at https://usesmartform.com/dashboard.
2. Clone, install, configure, run:
   ```bash
   git clone https://github.com/yanghuai123456/smartform-example-vite-vue.git
   cd smartform-example-vite-vue
   npm install
   cp .env.example .env
   # edit .env → VITE_SMARTFORM_FORM_ID=f_your_real_id
   npm run dev
   ```
3. Open http://localhost:5173, submit, check your dashboard.

## The form

`src/components/ContactForm.vue` is a Vue 3 SFC that POSTs JSON to SmartForm.

```vue
<script setup>
import { ref } from 'vue';

const FORM_ID  = import.meta.env.VITE_SMARTFORM_FORM_ID;
const ENDPOINT = 'https://api.usesmartform.com/api/v1/f';
const status   = ref('');

async function onSubmit(e) {
  e.preventDefault();
  status.value = 'Sending…';
  const data = Object.fromEntries(new FormData(e.currentTarget));
  try {
    const r = await fetch(`${ENDPOINT}/${FORM_ID}`, {
      method:  'POST',
      headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
      body:    JSON.stringify(data),
    });
    const body = await r.json();
    status.value = `Sent! submission_id=${body.submission_id} intent=${body.intent}`;
  } catch (err) {
    status.value = `Error: ${err.message}`;
  }
}
</script>

<template>
  <form @submit="onSubmit">
    <input v-if="!FORM_ID" /> <!-- placeholder -->
    <input name="name"  placeholder="Name"  required />
    <input name="email" type="email" placeholder="Email" required />
    <textarea name="message" placeholder="Message" required />
    <input type="text" name="_gotcha" tabindex="-1" autocomplete="off"
           style="position:absolute;left:-9999px" aria-hidden="true" />
    <button type="submit">Send</button>
    <p>{{ status }}</p>
  </form>
</template>
```

## How the API works

- `POST {endpoint}/api/v1/f/{form_id}` — JSON or form-data, no API key.
- Response: `{ success, message, submission_id, is_spam, intent, next_url }`.

For the full contract, see https://usesmartform.com/docs.

## Deploy

```bash
npm run build         # static output in ./dist
npx vercel --prod     # or netlify deploy --prod, wrangler pages deploy ./dist
```

Set `VITE_SMARTFORM_FORM_ID` as an env var in your hosting dashboard.

## License

MIT.
