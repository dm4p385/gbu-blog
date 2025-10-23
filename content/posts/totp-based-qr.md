+++
title = "Designing TOTP style QR Codes for Scale in Low Connectivity Environments"
date = "2025-10-23T12:00:00+05:30"
draft = false
tags = ["TOTP","engineering"]
categories = ["blog"]
summary = "By converting QR codes into time-synced tokens (like Google Auth), we cut server load, minimized retries, and made our fest app work flawlessly offline-first."
+++

## **Background**

During college, I was part of a club responsible for building the apps and websites used during BITS Pilani’s cultural and tech fests. These fests are massive multi-day events packed with competitions,´ concerts, food stalls, thousands of participants, and a chaotic influx of people entering and exiting the campus. Managing the end-to-end participant lifecycle without tech would have been a logistical nightmare.

So we turned everything into a digital flow:

- Event registration → scan your fest app QR
- Food stalls → order digitally, pick up using OTP
- Concert entry → scan & go
- Payments, tracking, validation → all digitized and handled by software

If there existed a logistical bottleneck, we tried to solve it with software.

But while building features is easy, building them to scale under hostile constraints (like terrible internet) is a whole different challenge.

---

## The Problem

Each user had a QR code tied to their profile. Scanning this QR allowed us to identify them for registration, food orders, or concert entry. Simple, right?

Except… if someone got hold of your QR, they could:

- Register you into paid events
- Use up your concert tickets

To prevent these kinds of attacks, we needed *time-bound* QR codes which had to expire periodically and be re-fetched from the server.

This introduced a new bottleneck: **QRs were being refreshed far too frequently, and every scan required server validation.**

And then came the worst part: **concert zones had horrible internet.**

Requests were slow, retries were frequent, and collectively 4000+ attendees aggressively refreshing their QR screens choked the already weak bandwidth.

We weren’t dealing with just *load,* we were dealing with **load + retries + terrible latency + bandwidth constraints**.

---

## **The Scale (Rough Guesstimates)**

Assuming a retry factor of ~3 per request due to poor connectivity:

| Action | Users | Retries | Total Requests |
| --- | --- | --- | --- |
| QR fetch | 4000 | ×3 | ~12,000 |
| QR scans | 4000 | ×2 | ~8,000 |
| **Total** |  |  | ~20,000 |

Spread over 1–2 hours = 3–6 req/sec on average.

But peak bursts (during panic refreshes) likely hit 20–50 req/sec under poor network conditions.

All of this… on a connection that could barely load a 4K YouTube video.

---

## **Constraints**

We needed a solution that:

- Drastically reduced client-server communication
- Still enforced expiry & security
- Worked even under flaky/no internet

---

## **The “Aha!” Moment**

I asked myself: *“Where have I seen a system that refreshes every 30s, works offline, and still stays secure?”*

And then it hit me: **Google Authenticator already implements this!**

How does it generate valid OTPs without any network communication?

It uses a **pre-shared secret key + time-based hashing (TOTP)**.

From a Reddit explanation I found:

> Using a shared secret and a current timestamp (rounded to 30-second windows), both client and server independently generate the same hash. Because time is shared, they remain in sync, even offline.
> 

Example:

If timestamp = `1505480619` and secret key = `1234`,

Then `hash(timestamp * key) → last 4 digits = OTP`.

After 30 seconds, a new OTP appears, but still matches on server side.

*No network calls. No refresh requests. No bandwidth load.*

---

### **The New Architecture**

| Before (Old Flow) | After (New Flow Inspired by TOTP) |
| --- | --- |
| QR expired → app hits server frequently | App locally generates QR (based on time + secret) |
| Every scan → server validates | On scan → single server validation |
| Bandwidth-heavy | Near-zero bandwidth except final validation |

So the solution was simple, upon login we will generate a private key on the backend and share it with the app so both the parties had one piece of the puzzle. 

Both client and server used this shared secret to generate a time-bound QR code.

Even if internet was slow or unavailable, the QR always appeared in time.
<div style="display:flex;gap:1rem;flex-wrap:wrap;margin:1rem 0;">
	<figure style="flex:1 1 48%;margin:0;">
		{{< img src="images/p1_old_flow.png" alt="The Old Flow" style="width:100%;height:auto;border-radius:8px;object-fit:cover;" >}}
		<figcaption style="font-size:0.9rem;color:var(--muted,#666);margin-top:0.4rem;">Old flow: server-heavy QR refresh</figcaption>
	</figure>
	<figure style="flex:1 1 48%;margin:0;">
		{{< img src="images/p1_new_flow.png" alt="The New Flow" style="width:100%;height:auto;border-radius:8px;object-fit:cover;" >}}
		<figcaption style="font-size:0.9rem;color:var(--muted,#666);margin-top:0.4rem;">New flow: time-synced QR generated locally</figcaption>
	</figure>
</div>

---

### **What Happened Next**

We implemented this approach just **3 days before launch**.

Had a few timestamp mismatches? Yes.

Expanded verification window to T-1 to T+1 minutes? Fixed.

Launched under stress? Absolutely.

- On concert day, QR codes loaded *instantly*.
- Entry lines moved faster than ever.
- Nobody complained about network issues.
- We even got live praise on our campus’ social media circles for how smooth the experience was.

---

### **Conclusion**

Sometimes, solving a scaling issue isn’t about adding more servers or throwing infra at the problem, it’s about rethinking the system itself.

In the end we were able to

- Minimize network usage by reducing the throughput.
- Preserved security by still retaining the expiry feature.
- Implemented all of this within just 3 days of the app launch!
- Handle ticketing worth 7.6 million INR without a hitch
- But most importantly… won user trust ♥️

At the end of the day, what sets great engineering apart is the ability to *build → execute → iterate* with clarity and purpose even when the clock is ticking. This is something that I was able to achieve a lot of times working for this club and is probably the reason why I fell in love with software engineering in the first place.