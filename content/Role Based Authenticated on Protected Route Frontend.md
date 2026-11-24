Date: 2026-09-23, Wednesday, 09:41 PM

Tags: [[Web Development]]

So the first time I learned about it was in [Mavorion Systems](https://mavorion.com/). They used to call /auth/me/ (or something similar) endpoint (in an app) on every redirect, and if it failed, we would redirect users to the login page. It is expensive. So I've always wondered how I can cheaply but securely protect my frontend pages. First of all, client rendered applications can be tampered with by malicious users, so the final line of defense is always protecting the backend. With that in mind, let's see how we can protect the frontend.

The holy grail of secure storage for browsers that I've known till date is HTTP Only Cookies.

So, the idea is simple. Backend issues a session or jwt token in the HTTP Only Cookie.

For a complete client app:
Hit the api /auth/me/ with the cookie attached and check if backend sends a good response. If it responds with 401, redirect to login. If not, flip the isAuthenticated state (you could store the state in react context or zustland) to true.

Malicious users may flip isAuthenticated to true but if they do that, they will definitely have to hit the protected API, which will definitely throw a 401, which should be intercepted by React and the flag isAuthenticated should be set to false and the user should be sent to the login page.

For a SSR'ed app using NextJS/NuxtJS or any other server rendering supported app.
Here, we can run middlewares that run entirely on the NextJS server. We can also define environment variables that will never be exposed to the client. So with that approach, we can add the JWT secret from the backend's env vars to FE's env var. They should be in sync. Now the NextJS server can simply extract the HTTP only cookie (the jwt token) and verify it it was tampered with completely on its own server without talking to the backend. We can do this on every page redirect in the middleware. With session tokens, I believe something similar should be done.