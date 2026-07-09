Title: Stop Asking, Start Listening: The Magic of Webhooks
Date: 2026-07-09
Category: Programming
Tags: LangChain, LLM, AgenticAI
Slug:stop-asking-start-listening-the-magic-of-webhooks

Have you ever ordered food online and kept refreshing the app every 30 seconds to see if your delivery guy has left the restaurant yet?

Annoying, right?

Now imagine instead that the app just *pings you the second something changes*. No refreshing. No checking. It just... tells you.

That, in a nutshell, is what a **webhook** does for computer systems. And once you understand it, you'll start noticing it everywhere — in payment systems, chat apps, e-commerce stores, and pretty much every modern app that "just knows" when something happened.

Let's break it down together.


# **First, the problem webhooks solve**

Imagine two apps that need to talk to each other. Let's say your online store needs to know the moment a customer's payment goes through.

There are two ways to find this out:

**Option 1: Keep asking.**
Your store's server asks the payment company, "Hey, did the payment go through yet? How about now? Now?" — over and over, every few seconds. This is called **polling**, and it's exactly like refreshing that delivery app manually.

It works, but it's wasteful. You're making thousands of requests, most of which just get a boring "nothing new" reply.

**Option 2: Get a notification.**
Instead of asking again and again, your store simply says, "Hey payment company, when something happens, just call this specific phone number of mine and tell me." Then it relaxes and waits.

This second approach — where one system automatically notifies another the instant something happens — is a **webhook**.

---

# **So what exactly *is* a webhook?**

In plain English:

  > A webhook is an automatic message one app sends to another app when a specific event happens.

Think of it like a doorbell. You don't stand at your window all day checking if someone has arrived. Instead, you set up a doorbell (the webhook), and whenever someone shows up (the event), it rings automatically and tells you.

Technically, a webhook is just a small message (usually in a format called **JSON**, which is just neatly organized text data) sent over the internet to a specific web address you provide in advance. That address is often called a **callback URL** or **endpoint**.

No jargon panic here — just remember:
- **Event** = something happening (a payment, a new sign-up, a form submission)
- **Endpoint/URL** = the "address" where you want to be notified
- **Payload** = the actual message/data that gets sent


# **How is this different from a regular API?**

Good question, because they look similar but work in opposite directions.

- A normal **API call** is like *you* calling someone to ask a question. You reach out, and you wait for an answer.
- A **webhook** flips that. The *other* system calls *you*, without you having to ask.

That's why people often describe webhooks as a **"reverse API"** or "an API that calls you back."


# **Okay, but how do you actually build one?**

This is where it gets fun, because building a webhook system involves two sides — like a phone call needs both a caller and someone answering.

# **Side 1: The Sender (the app announcing events)**

If you're building the app that will *send* the notifications, you need to:

1. **Decide what events matter.** For example: "new order placed" or "user cancelled subscription."
2. **Let other developers register their URL.** They tell you, "Here's my endpoint — send updates here."
3. **When the event happens, send a message.** Your server automatically makes a small HTTP request (usually a `POST` request) to that saved URL, carrying the event details as data.

Simple version: *Event happens → grab the saved address → send a package of info there.*

Here's what that looks like in actual code (using Node.js, a popular way to write server-side JavaScript):

    // SENDER SIDE — sending a webhook when an event happens

    const axios = require('axios'); // a simple tool for making web requests

    // This URL was given to us earlier by the receiving app
    const receiverUrl = 'https://mystore.com/webhooks/payment';

    async function notifyPaymentSuccess(orderDetails) {
      try {
        // Send a POST request with the event data as the "payload"
        await axios.post(receiverUrl, {
          event: 'payment.succeeded',
          orderId: orderDetails.id,
          amount: orderDetails.amount,
          customerEmail: orderDetails.email,
          timestamp: new Date().toISOString()
        });
        console.log('Webhook sent successfully!');
      } catch (error) {
        console.error('Failed to send webhook:', error.message);
      }
    }

    // Call this function whenever a payment actually succeeds
    notifyPaymentSuccess({
      id: '1234',
      amount: 42,
      email: 'customer@example.com'
    });

Nothing scary here — it's just a package of information (`event`, `orderId`, `amount`, etc.) being sent to a saved address.

# **Side 2: The Receiver (the app listening for events)**

If you're building the app that *receives* webhook notifications, you need to:

1. **Create an endpoint (a URL) that can accept incoming messages.** This is usually a small piece of backend code — for example, a route like `/webhooks/payment-received`.
2. **Read and process the incoming data.** Once the message arrives, your code decides what to do — update a database, send an email, trigger another action.
3. **Reply quickly.** You're expected to respond fast (usually within a few seconds) just to confirm "Got it!" — even if the actual processing happens afterward. Think of it like signing for a package before unpacking it.

Here's a simple receiver built with Express (a popular Node.js framework):

    // RECEIVER SIDE — listening for incoming webhooks

    const express = require('express');
    const app = express();
    app.use(express.json()); // lets us read incoming JSON data

    // This is the endpoint we told the sender about
    app.post('/webhooks/payment', (req, res) => {
      const event = req.body;

      console.log('Webhook received:', event);

      if (event.event === 'payment.succeeded') {
        // Do something useful — update your database, send an email, etc.
        console.log(`Order ${event.orderId} marked as paid.`);
      }

      // Respond quickly to say "message received!"
      res.status(200).send('OK');
    });

    app.listen(3000, () => {
      console.log('Listening for webhooks on port 3000...');
    });


That's really it. When Stripe (or any sender) hits this URL with a `POST` request, this code wakes up, reads the data, does something with it, and replies "OK."

# **A quick real-world example**

Let's say you use an online payment tool like Stripe.

- You (the developer) tell Stripe: "Whenever a payment succeeds, send the details to `https://mystore.com/webhooks/payment`."
- A customer pays.
- Stripe automatically sends a small JSON message to that URL saying, "Payment of $42 succeeded for order #1234."
- Your server receives it, updates the order status to "Paid," and emails the customer a receipt — all without a human lifting a finger.

That entire flow — no refreshing, no manual checking — happens in under a second.

---

# **One important safety note**

Since webhooks let outside systems send data straight into your app, it's important to make sure the message *actually* came from who it claims to be from — not a stranger pretending to be Stripe.

This is usually done using a **secret signature** — a bit like a wax seal on a letter. The sender signs the message with a secret key, and your server checks that signature before trusting it.

Here's roughly what that check looks like in code:

    const crypto = require('crypto');

    function isValidSignature(payload, receivedSignature, secretKey) {
      // Recreate the signature ourselves using the same secret key
      const expectedSignature = crypto
        .createHmac('sha256', secretKey)
        .update(JSON.stringify(payload))
        .digest('hex');

      // If it matches what the sender sent us, we know it's legit
      return expectedSignature === receivedSignature;
    }


You don't need to memorize the technical details right now — just know that **verifying webhook signatures is a standard, important step**, not an optional extra.

---

# **The key takeaway**

Webhooks are simply a way for apps to **notify each other automatically**, instead of one app constantly nagging the other for updates.

If APIs are like *asking* a question, webhooks are like *getting a phone call the moment there's news*.

Next time an app seems to magically "know" the second something happens — a payment, a message, a delivery update — you'll know exactly what's going on behind the scenes: a quiet little webhook, doing its job.

**In short:** stop polling, start listening. Your servers (and your sanity) will thank you.