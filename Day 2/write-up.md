# Login Request Write up

**Site under test:** automationexercise.com (public QA practice site)
**Feature tested:** Login form submission
**Tool used:** Chrome DevTools → Network tab (HAR export)

## Observation 1 — Loading the login page

- Request: `GET https://automationexercise.com/login`
- Status: `200 OK`
- The browser fetching the login form itself. No credentials are involved at this stage; the response is just the HTML page containing the form.

## Observation 2 — Submitting the form

- Request: `POST https://automationexercise.com/login`
- Status: `302 Found`
- Sent about 13 seconds after the GET, consistent with the time taken to fill in the form.
- Content-Type: `application/x-www-form-urlencoded`, and `sec-fetch-mode: navigate` — this is a traditional full-page form submission, not a JavaScript/AJAX-based login.
- The POST body carries three fields: `csrfmiddlewaretoken`, `email`, and `password`.
- The response includes `Location: /` — a genuine server-side redirect to the homepage after a successful login. This is the Post/Redirect/Get pattern, which is why refreshing the page afterward doesn't prompt "Confirm form resubmission."

## Session/cookie behavior — still to confirm

Both the GET and POST requests already carried the same `sessionid` cookie value before the form was even submitted, meaning a session existed prior to login. The open question is whether the POST response's `Set-Cookie` issues a **new** `sessionid` after authentication, or reuses the one that was already there:

- A new sessionid after login is the expected, secure behavior — Django's `login()` rotates the session key by default specifically to prevent session fixation.
- The same sessionid carried through would be worth flagging as an actual finding, not just a footnote.

This can't be read from a HAR export — Chrome leaves `Set-Cookie` out of it by default. [Confirm this directly from the Cookies tab on the POST request in live DevTools, then fill in the actual result here.]
