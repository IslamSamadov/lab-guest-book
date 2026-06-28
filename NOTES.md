# Detective notes

This is your deliverable. We grade the *thinking*, not the word count. Be honest
about what confused you and what unstuck you. Short and real beats long and fake.

---

## Bug 1: the page reloads and my message vanishes

**What I think is wrong (before fixing):**
Hitting "Sign" triggers the browser's default behavior from the pre-JavaScript era. The browser treats the form submission as a command to package the inputs and navigate away to a new URL, which forces a full page reload and wipes out all local React state.

**What did I ask the AI / what did I look up:**
I asked the AI how to stop the form from reloading the page and how to resolve an environmental terminal error (`'next' is not recognized`).

**What was the solution:**
The solution was adding `e.preventDefault()` as the very first line inside the `handleSubmit(e)` function. This explicitly tells the browser to cancel its native navigation routine so JavaScript can handle the event. (I also had to run a clean `npm install` because the local dependencies hadn't finished installing initially).

---

## Bug 2: even without the reload, nothing gets saved

**What I think is wrong (before fixing):**
The original code was firing a plain `await fetch("/api/messages")` without any configuration. The Network tab showed this was executing a `GET` request with no data body payload, whereas the route handler explicitly required a `POST` request containing a JSON body.

**What did I ask the AI / what did I look up:**
I reviewed the lab README instructions regarding the anatomy of a `POST` request and checked why the request wasn't showing up in my Network tab initially.

**What was the solution:**
I rebuilt the `fetch` call by passing an options object as the second argument. I explicitly specified `method: "POST"`, added a `headers` object containing `"Content-Type": "application/json"`, and passed a stringified JSON object containing the `name` and `text` state variables into the `body`. Finally, I appended the successful server response directly to the `messages` state array so it updated instantly on screen.

---

## Closing reflection

- **Why does a `<form>` reload the page by default, and what does `preventDefault` actually prevent?**
  Forms predate modern JavaScript frameworks. Their original native job was to bundle user fields, send them to a server endpoint, and load a brand new page with the response. `preventDefault()` steps in to stop that automatic browser navigation/refresh from executing.

- **Why can a GET not carry your message, while a POST can?**
  A `GET` request is designed to retrieve data from a server, meaning its parameters are appended openly into the URL string and it lacks a data payload container. A `POST` request is meant to submit data to be stored, providing a dedicated `body` stream that securely transports complex payloads.

- **What is the `Content-Type` header telling the server, and what breaks without it?**
  The `Content-Type: application/json` header functions as an explicit blueprint telling the server, "The raw bytes coming your way are formatted as JSON, parse them accordingly." Without it, the server's `request.json()` method cannot safely assume the incoming format and will crash or reject the request.

---

**Live URL (Vercel):** *Not deployed (working locally in development environment)*