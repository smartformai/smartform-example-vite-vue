# Vue 3 (Vite) contact form — Formspree alternative with AI spam filtering

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
## Related examples
[Vite + React contact form](https://github.com/yanghuai123456/smartform-example-vite-react) | [Angular contact form](https://github.com/yanghuai123456/smartform-example-angular) | [smartform-js SDK](https://github.com/yanghuai123456/smartform-js)


## FAQ

### Why use this instead of Formspree?

Both SmartForm and Formspree let you POST a plain HTML form to a hosted
endpoint with no backend. SmartForm adds an AI spam filter (not just
honeypots), AI intent classification (`sales` / `support` / `inquiry`)
and high-value lead detection, with a free tier that includes the spam
filter. Formspree charges per submission; SmartForm's spam filter is
free on every plan.

### Is there a free tier?

Yes. AI spam filtering is enabled by default on every plan. AI intent
classification and high-value lead detection require a paid plan (Pro
or Business) — the dashboard enforces this and returns HTTP 402 if
you try to enable them on a free workspace.

### Do I need an API key?

No. The form posts directly to a public endpoint using only an 8-char
form ID, which is non-enumerable. The example also includes a hidden
`_gotcha` honeypot field so naive bots cannot submit.

### Do I need a backend?
No. The form posts JSON to the public endpoint. The example is a single Vue 3 SFC with inline status.

## Related examples
[Vite + React contact form](https://github.com/yanghuai123456/smartform-example-vite-react) | [Angular contact form](https://github.com/yanghuai123456/smartform-example-angular) | [smartform-js SDK](https://github.com/yanghuai123456/smartform-js)


## License

MIT.

