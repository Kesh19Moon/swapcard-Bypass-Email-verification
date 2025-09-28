# swapcard-Bypass-Email-verification
🔐 Swapcard Password Reset — The One Where the Reset Button Believes Lies

TL;DR — I told the password reset page a tiny fib and it believed me. The app moved me to the password-reset screen and even said “reset email sent” for an email that doesn’t exist. Not a Hollywood hack, but definitely a logic facepalm. Reported responsibly.

🧩 Summary (short & spicy)

While testing Swapcard’s team login/reset flow I found that the app does not properly validate whether an email actually exists before allowing a password reset flow to continue. By tampering with the validation response, the client will happily proceed to the password page and show a “reset email sent” message — even for unregistered emails.

This breaks the expected flow, enables user-enumeration patterns, and could help an attacker craft phishing or social-engineering campaigns.

😅 What went wrong (in plain words)

Normally:

You enter your email → server checks if it exists → server says “yes/no”.

If “no”, the process should stop or respond with a generic message.

In this case:

The client accepted a forged “yes” and let me land on the password-reset page.

The app even confirms that a reset mail was sent (it wasn’t).

Not dramatic, but enough to make the login flow lie to users — and lie to users is never great.

🔬 How to reproduce (high-level, ethical)

(This is intentionally non-exploitative — do not use on accounts you don’t own. Use only in safe, authorized testing contexts.)

Go to the team login / password-reset flow on the site.

Submit an unregistered email.

Observe the normal client behavior (it should block progression).

During testing, a crafted/mocked server response can be shown to the client that indicates the email “exists” — the client then proceeds to the password/reset page and shows a confirmation message, even though no reset email was actually sent.

Note: I used sanitized test data and only performed non-destructive checks. Don’t run this against live accounts without authorization.

🧾 Sanitized PoC (what the client sees)

Server says “no” (sanitized example):
[{
  "data": {
    "lookup": {
      "alreadyRegistered": false,
      "hasAPassword": false
    }
  }
}]

Client is persuaded (forged/sanitized example):
[{
  "data": {
    "lookup": {
      "alreadyRegistered": true,
      "hasAPassword": true
    }
  }
}]

(The above is illustrative and sanitized — do not use this to attack systems.)

🚨 Risks (why this matters)

User enumeration — attackers can infer which emails are registered by comparing flows and behaviors.

Phishing & Social Engineering — false confirmations make it easier to trick users into clicking fake links.

Password reset abuse — combined with other flaws, could lead to account takeover.

Trust & UX damage — users told “email sent” when none was reduces trust and support burden.

🛠️ Recommended fixes (what I’d do if I were on the team)

Server-side truth only: never let the client decide or accept a “valid” state based solely on client-side signals.

Generic response messaging: respond to reset requests with:

“If an account exists with this email, we have sent reset instructions.”
This prevents direct confirmation of existence.

Integrity checks: validate state transitions server-side (don’t advance user flows based on client-only assumptions).

Rate-limiting & logging: throttle repeated reset attempts and log suspicious flows.

Unique, single-use tokens: ensure reset tokens are unique and expire quickly; avoid token collisions from array- or multi-request behavior.

✅ Responsible disclosure

Reported to Swapcard’s security team (date: insert date here).

All testing used test emails or identifiers owned by me. No real user data was accessed or exposed.

If you work at Swapcard: hi — I’ve got sanitized PoC material and a calm coffee if you want details.

🧩 Final thoughts (funny but true)

This isn’t a blockbuster exploit. It’s a logic gap — the kind that makes you smile, sigh, and then write a GitHub page about it. Little lies in the UX can make big problems later. Patch the truth checks, and the flow behaves again.

If you work in platform security, product, or QA and want sanitized notes or a short demo, ping me,

# 👀 Bonus Content: Job/Intern Hunt

Also… real talk: I don’t want to be in a digital cave forever, hacking away alone in the hoodie life. 😅

If your company is legit and needs someone who can:

Hunt bugs like a caffeinated ninja

Make your security systems slightly less embarrassing with petty bugs

Occasionally write fun stories about exploits

…then I’m open to SOC, penetration testing, or any junior-level positions or internships where I can learn more than I do alone.

DM me on LinkedIn or email me from a real company address. Please—no fake recruiters, no scams. Let’s make cybersecurity a little safer… and a little less lonely.

💻 Written by Keshor Murugesan keshorpuresoul@gmail.com Cybersecurity enthusiast, bug hunter, patient data guardian, and aspiring team player.
