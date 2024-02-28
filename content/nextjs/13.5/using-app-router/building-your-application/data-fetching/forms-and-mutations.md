---
linkTitle: "Forms and Mutations"
title: "Data Fetching: Forms and Mutations | Next.js"
description: "Learn how to handle form submissions and data mutations with Next.js."
weight: 3
type: docs
---

# Forms and Mutations

Forms enable you to create and update data in web applications. Next.js provides a powerful way to handle form submissions and data mutations using **Server Actions**.

## How Server Actions Work {#how-server-actions-work}

With Server Actions, you don't need to manually create API endpoints. Instead, you define asynchronous server functions that can be called directly from your components.

> **🎥 Watch:** Learn more about forms and mutations with the App Router → [YouTube (10 minutes) ↗](https://youtu.be/dDpZfOQBMaU?si=cJZHlUu_jFhCzHUg).
> 

Server Actions can be defined in Server Components or called from Client Components. Defining the action in a Server Component allows the form to function without JavaScript, providing progressive enhancement.

Enable Server Actions in your `next.config.js` file:


next.config.js
```
module.exports = {
  experimental: {
    serverActions: true,
  },
}
```

> **Good to know:**
> - Forms calling Server Actions from Server Components can function without JavaScript.
> - Forms calling Server Actions from Client Components will queue submissions if JavaScript isn't loaded yet, prioritizing client hydration.
> - Server Actions inherit the [runtime](/nextjs/13.5/using-app-router/building-your-application/rendering/edge-and-nodejs-runtimes) from the page or layout they are used on.
> - Server Actions work with fully static routes (including revalidating data with ISR).
> 

## Revalidating Cached Data {#revalidating-cached-data}

Server Actions integrate deeply with the Next.js [caching and revalidation](/nextjs/13.5/using-app-router/building-your-application/caching) architecture. When a form is submitted, the Server Action can update cached data and revalidate any cache keys that should change.

Rather than being limited to a single form per route like traditional applications, Server Actions enable having multiple actions per route. Further, the browser does not need to refresh on form submission. In a single network roundtrip, Next.js can return both the updated UI and the refreshed data.

View the examples below for [revalidating data from Server Actions](/nextjs/13.5/using-app-router/building-your-application/data-fetching/forms-and-mutations#revalidating-data).

## Examples {#examples}

### Server-only Forms {#server-only-forms}

To create a server-only form, define the Server Action in a Server Component. The action can either be defined inline with the `"use server"` directive at the top of the function, or in a separate file with the directive at the top of the file.


app/page.tsx
```
export default function Page() {
  async function create(formData: FormData) {
    'use server'
 
    // mutate data
    // revalidate cache
  }
 
  return <form action={create}>...</form>
}
```

> **Good to know**: `<form action={create}>` takes the [FormData ↗](https://developer.mozilla.org/docs/Web/API/FormData/FormData) data type. In the example above, the FormData submitted via the HTML [`form` ↗](https://developer.mozilla.org/docs/Web/HTML/Element/form) is accessible in the server action `create`.
> 

### Revalidating Data {#revalidating-data}

Server Actions allow you to invalidate the [Next.js Cache](/nextjs/13.5/using-app-router/building-your-application/caching) on demand. You can invalidate an entire route segment with [`revalidatePath`](/nextjs/13.5/using-app-router/api-reference/functions/revalidatePath):


app/actions.ts
```
'use server'
 
import { revalidatePath } from 'next/cache'
 
export default async function submit() {
  await submitForm()
  revalidatePath('/')
}
```

Or invalidate a specific data fetch with a cache tag using [`revalidateTag`](/nextjs/13.5/using-app-router/api-reference/functions/revalidateTag):


app/actions.ts
```
'use server'
 
import { revalidateTag } from 'next/cache'
 
export default async function submit() {
  await addPost()
  revalidateTag('posts')
}
```

### Redirecting {#redirecting}

If you would like to redirect the user to a different route after the completion of a Server Action, you can use [`redirect` ↗](https://nextjs.org/docs/app/api-reference/functions/redirect.html) and any absolute or relative URL:


app/actions.ts
```
'use server'
 
import { redirect } from 'next/navigation'
import { revalidateTag } from 'next/cache'
 
export default async function submit() {
  const id = await addPost()
  revalidateTag('posts') // Update cached posts
  redirect(`/post/${id}`) // Navigate to new route
}
```

### Form Validation {#form-validation}

We recommend using HTML validation like `required` and `type="email"` for basic form validation.

For more advanced server-side validation, use a schema validation library like [zod ↗](https://zod.dev/) to validate the structure of the parsed form data:


app/actions.ts
```
import { z } from 'zod'
 
const schema = z.object({
  // ...
})
 
export default async function submit(formData: FormData) {
  const parsed = schema.parse({
    id: formData.get('id'),
  })
  // ...
}
```

### Displaying Loading State {#displaying-loading-state}

Use the `useFormStatus` hook to show a loading state when a form is submitting on the server. The `useFormStatus` hook can only be used as a child of a `form` element using a Server Action.

For example, the following submit button:


app/submit-button.tsx
```
'use client'
 
import { experimental_useFormStatus as useFormStatus } from 'react-dom'
 
export function SubmitButton() {
  const { pending } = useFormStatus()
 
  return (
    <button type="submit" aria-disabled={pending}>
      Add
    </button>
  )
}
```

`<SubmitButton />` can then be used in a form with a Server Action:


app/page.tsx
```
import { SubmitButton } from '@/app/submit-button'
 
export default async function Home() {
  return (
    <form action={...}>
      <input type="text" name="field-name" />
      <SubmitButton />
    </form>
  )
}
```

### Error Handling {#error-handling}

Server Actions can also return [serializable objects ↗](https://developer.mozilla.org/docs/Glossary/Serialization). For example, your Server Action might handle errors from creating a new item:


app/actions.ts
```
'use server'
 
export async function createTodo(prevState: any, formData: FormData) {
  try {
    await createItem(formData.get('todo'))
    return revalidatePath('/')
  } catch (e) {
    return { message: 'Failed to create' }
  }
}
```

Then, from a Client Component, you can read this value and display an error message.


app/add-form.tsx
```
'use client'
 
import { experimental_useFormState as useFormState } from 'react-dom'
import { experimental_useFormStatus as useFormStatus } from 'react-dom'
import { createTodo } from '@/app/actions'
 
const initialState = {
  message: null,
}
 
function SubmitButton() {
  const { pending } = useFormStatus()
 
  return (
    <button type="submit" aria-disabled={pending}>
      Add
    </button>
  )
}
 
export function AddForm() {
  const [state, formAction] = useFormState(createTodo, initialState)
 
  return (
    <form action={formAction}>
      <label htmlFor="todo">Enter Task</label>
      <input type="text" id="todo" name="todo" required />
      <SubmitButton />
      <p aria-live="polite" className="sr-only">
        {state?.message}
      </p>
    </form>
  )
}
```

### Optimistic Updates {#optimistic-updates}

Use `useOptimistic` to optimistically update the UI before the Server Action finishes, rather than waiting for the response:


app/page.tsx
```
'use client'
 
import { experimental_useOptimistic as useOptimistic } from 'react'
import { send } from './actions'
 
type Message = {
  message: string
}
 
export function Thread({ messages }: { messages: Message[] }) {
  const [optimisticMessages, addOptimisticMessage] = useOptimistic<Message[]>(
    messages,
    (state: Message[], newMessage: string) => [
      ...state,
      { message: newMessage },
    ]
  )
 
  return (
    <div>
      {optimisticMessages.map((m, k) => (
        <div key={k}>{m.message}</div>
      ))}
      <form
        action={async (formData: FormData) => {
          const message = formData.get('message')
          addOptimisticMessage(message)
          await send(message)
        }}
      >
        <input type="text" name="message" />
        <button type="submit">Send</button>
      </form>
    </div>
  )
}
```

### Setting Cookies {#setting-cookies}

You can set cookies inside a Server Action using the [`cookies`](/nextjs/13.5/using-app-router/api-reference/functions/cookies) function:


app/actions.ts
```
'use server'
 
import { cookies } from 'next/headers'
 
export async function create() {
  const cart = await createCart()
  cookies().set('cartId', cart.id)
}
```

### Reading Cookies {#reading-cookies}

You can read cookies inside a Server Action using the [`cookies`](/nextjs/13.5/using-app-router/api-reference/functions/cookies) function:


app/actions.ts
```
'use server'
 
import { cookies } from 'next/headers'
 
export async function read() {
  const auth = cookies().get('authorization')?.value
  // ...
}
```

### Deleting Cookies {#deleting-cookies}

You can delete cookies inside a Server Action using the [`cookies`](/nextjs/13.5/using-app-router/api-reference/functions/cookies) function:


app/actions.ts
```
'use server'
 
import { cookies } from 'next/headers'
 
export async function delete() {
  cookies().delete('name')
  // ...
}
```

See [additional examples](/nextjs/13.5/using-app-router/api-reference/functions/cookies#deleting-cookies) for deleting cookies from Server Actions.
