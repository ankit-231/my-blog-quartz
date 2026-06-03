Date: 2026-06-03, Wednesday, 10:06 AM

Tags: [[Tailscale]], [[Useful Things]]

I first learned about it while setting up a VPS for my second [[freelance]] project, that is an e-commerce store for AlulaFPV (a drone manufacturing company and retailer based in Nepal). The site should be still up: [AlulaFPV Store - FPV Drones & Accessories in Nepal](https://store.alulafpv.com/).

I am currently interested in mobile app development. Coming from a web dev background, for local development, I had the leverage of hosting my backend and frontend on localhost and I did not have to think about cors errors until on production.

But for mobile development in React Native [Expo](http://expo.dev/), I have to host backend on local network (like 192.168.1.55) instead of localhost (127.0.0.1).

So I've done a bit of research (on google and LLMs, of course) and found about [[Tailscale]] is perfect for these scenarios.

I'd logged into [Tailscale](https://login.tailscale.com/login) a long time ago and also installed it on my Ubuntu 22.04.5 LTS.

But still here's how you can install tailscale. This is directly from [Install Tailscale on Linux · Tailscale Docs](https://tailscale.com/docs/install/linux). They have guide for other OS as well of course.
```sh
curl -fsSL https://tailscale.com/install.sh | sh
```

Then start the client:
```sh
sudo tailscale up
```

It'll give you a link like: https://login.tailscale.com/a/random_token_here.

Open it and login to your account.

Install tailscale app on your phone, I'm using android.

See guide: [Install Tailscale on Android · Tailscale Docs](https://tailscale.com/docs/install/android).

All I did was install it from play store and login with same account as the one used in my laptop.

On your laptop, open terminal and do this to verify connectivity:
```sh
tailscale ip -4
```

It will give you your tailscale's IP address.

Status of tailscale can be seen with:
```sh
tailscale status
```
You'll see all your connected devices listed. i can see my 
```txt
➜  ~ tailscale status
100.101.102.103    ankit-ubuntu  demo@  linux    -  
100.88.45.67  ankit-phone    demo@  android  - 
```

Now, run your django server:
```sh
python manage.py runserver 100.101.102.103:8000 # laptop's tailscale ip
```

OR

To accept connections on port 8000 from any network interface available on this computer.
```sh
python manage.py runserver 0.0.0.0:8000 # to listen on all
```

If you don't understand what `0.0.0.0:8000` does like me. See this:

For example, suppose your Ubuntu machine has:
```txt
127.0.0.1      (localhost)
192.168.1.50   (LAN)
100.101.102.103 (Tailscale)
```

If your app listens on:

```txt
127.0.0.1:8000
```

only the local machine can connect:

```sh
curl http://127.0.0.1:8000
```

But if it listens on:

```txt
0.0.0.0:8000
```

then all of these can work:

```txt
http://127.0.0.1:8000
http://192.168.1.50:8000
http://100.101.102.103:8000
```

Now you can open your laptop's tailscale ip address in phone's browser with port configured in Django's server and Django's page should open in your phone.

Open 100.101.102.103:8000 on phone's chrome.

It works.