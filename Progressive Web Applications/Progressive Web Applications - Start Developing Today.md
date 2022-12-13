---
id: cd7d5df4-fdac-47c8-b271-d482193d63f4
title: Progressive Web Applications - Start Developing Today
published: true
postedOn: 05/15/2019
image: .attachments/7b066af14aa42c569b3de8d64fa46db40a1ff53b.png
category: pwa
tags: javascript, pwa
description: "When I first heard about Progressive Web Applications I exclaimed: \"This is it! That's how application development should look like\". Truly cross-platform, one solution for every platform, from mobile to desktop. Fast, offline, responsive, installable and many more. Then I decided to build my very first PWA! In this post, I want to share all my findings which accumulate my experience with Progressive Web Applications. Let's the story begin."
---

# Introduction
As a developer, by my nature, I'm really eager for code reuse and for different kind of universal approaches.

When I first heard about Progressive Web Applications I exclaimed: "This is it! That's how application development should look like". Truly cross-platform, one solution for every platform, from mobile to desktop. Fast, offline, responsive, installable and many more.

Then I decided to build my very first PWA! In this post, I want to share all my findings which accumulate my experience with Progressive Web Applications. Let's the story begin.

# PWA

I want to start from the definition of what is actually Progressive Web Applications. First of all, PWA is not a programming language and not a framework; it's a set of web standards. For better understanding what it is, read the PWA characteristics below:

* **Discoverable** — since PWA just a website, you can discover it on the web using search engines like Google and Bing.

* **Installable** — thanks to Web App Manifest for this, you can install a regular website for offline use and pin it's icon to desktop. 

* **Re-engageable** — even when the PWA is not open you can still receive push notifications, thanks to service workers. 

* **Network Independent** — the PWA can work offline and on a slow network. This is achieved using service workers, which can cache static assets as well as API calls.

* **Progressive** — work for every user, regardless of browser choice because they're built with progressive enhancement as a core tenet. It means if the browser missing some features, try to substitute them with something else.

* **Safe** — data delivered over HTTPS protocol, which is a secure channel. It means that you need an SSL certificate for your app, but this is not a problem nowadays. You can get a free certificate with such services as [Let's Encrypt](https://letsencrypt.org/).

* **Responsive** — thanks to CSS media queries, it's not difficult to create responsive UI, which will look good on different screen sizes from desktop to phones.

* **Linkable** — again, since PWA is a website, you can share it with your friends using URL.

Basically, in a few words, you can transform your website and use as "native" mobile/desktop application with all advantages like offline work, push notifications, standalone mode, etc.

<!--more-->

# 10 Progressive Web Applications that you can use right now

Did you become Interested? Let's check it out who on the market has PWA already. Below, I gathered the list of most prominent Progressive Web Applications which you can try right now:

1. Twitter https://mobile.twitter.com/

2. Uber https://m.uber.com/

3. Google Photos https://photos.google.com/

4. Instagram https://www.instagram.com/

5. Starbucks https://app.starbucks.com/

6. Tinder https://tinder.com/

7. Fandango Movies https://mobile.fandango.com/

8. OLX https://www.olx.in/

9. Pinterest https://www.pinterest.com/

10. Telegram https://web.telegram.org/

# Success stories

Here I would like to showcase some success stories, how PWA can transform the business:

### [Twitter Lite](https://developers.google.com/web/showcase/2017/twitter)
Twitter Lite (PWA) saw a 75% increase in Tweets sent, 65% in pages per session, and a 20% decrease in bounce rate.

PWA version of Twitter is now the fastest and most reliable way to use Twitter. The web app rivals the performance of the native app but requires less than 3% of the device storage space compared to Twitter for Android devices.

### [Uber](https://eng.uber.com/m-uber/)
Uber’s PWA is designed to be fast even on 2G network. The core app is only 50KB gzipped and takes less than 3 seconds to load on 2G (250kB/s, 300ms latency) networks.

### [Pinterest](https://medium.com/@Pinterest_Engineering/a-one-year-pwa-retrospective-f4a2f4129e05)
Pinterest rebuilt its mobile website as a Progressive Web Application. Weekly active mobile users have increased 103 percent year-over-year overall. Session length increased by 296 percent, the number of Pins seen increased by 401 percent.
Logins increased by 370 percent and new signups increased by 843 percent year-over-year.

Since Pinterest shipped the new web experience, PWA has become the top platform for new signups. Less than 6 months since fully shipping, they had 800 thousand weekly users using PWA like a native app (from their home screen).

### [Tinder](https://medium.com/@addyosmani/a-tinder-progressive-web-app-performance-case-study-78919d98ece0)
Tinder reduced load time from 11.91 seconds to 4.69 seconds with their new Progressive Web App. The PWA is 90% smaller than Tinder’s native Android application. User engagement is up across the board on the PWA.

# Minimal Progressive Web Application

Building a simple PWA is not rocket science. Consider the example below as "Hello World" application.
For the minimal Progressive Web Application, we need the following:

* Web App Manifest (manifest.json)
* Application icon (logo-512.png)
* Service worker (serviceworker.js)
* Html page (index.html)
* HTTPS

## manifest.json
You can think of **Web App Manifest** file as a collection of properties for your application.
Here you can specify the name of your app, start URL, icons for pinning to home screen, colors of a splash screen and status bar and many many more.
This is not a complete list of what you can do, just the minimum one. To see all properties, please read the official [specification](https://www.w3.org/TR/appmanifest/).

```json
{
 "name": "First PWA",
 "short_name": "PWA",
 "start_url": "/",
 "display": "standalone",
 "theme_color": "#ffffff",
 "background_color":"#ffffff",
 "icons": [
   {
     "src": "logo-512.png",
     "sizes": "512x512",
     "type": "image/png"
   }]
}
```

## serviceworker.js

As you should already know, a service worker is a piece of javascript code that your browser runs in the background and separate from the web page. As for today, it can perform such operations like push notifications and background sync. In the example below, we will cache index.html file, that will enable offline mode for your first PWA application.

```js
// "Install" event handler will be invoked only once when you first time navigate to the page
self.addEventListener('install', function(event) {
 event.waitUntil(
   caches.open('app-cache').then(function(cache) {
     // Adds index.html page to the cache
     return cache.add('index.html');
   })
 );
});

// Will be invoked for every network call: XHR, static resources, etc.
self.addEventListener('fetch', function(event) {
 event.respondWith(
   // For every request checks matching value in the cache
   caches.match(event.request).then(function(response) {
     // Returns cached response; otherwise, makes an actual network call
     return response || fetch(event.request);
   })
 );
});

```

If you would like to dive deeper into service worker development, please read the official [specification](https://w3c.github.io/ServiceWorker).

## index.html
Finally, the last piece to tie everything together. Here you can find a link to manifest file. Using feature detection approach, we register service worker script.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#ffffff">
    <link rel="manifest" href="/manifest.json">
    <title>First PWA</title>
  </head>
  <body>
    <h1>Hello PWA!</h1>
    <script>
      if('serviceWorker' in navigator) {
        navigator.serviceWorker.register('/serviceworker.js');
      }
    </script>
  </body>
</html>
```

Congratulations, now you should understand the core principles of building PWA. Let's move on!

# My experience

When you start learning a new technology, what do you usually make? "Hello World" or something similar, right?

In my case with PWA, I decided to make something more complicated. Please meet [**MultiCalc**](https://about.multicalculator.app) - a powerful, modern, multifunctional calculator made for everyone.

![Multicalc](.attachments/f82133052f93900de54f02ce2771079b3e4cc014.png)

The project was implemented using Angular 7 and TypeScript. For hosting, I chose Azure Storage (Website feature) + Verizon CDN for SPA rauting. In terms of PWA features like service workers, I didn't do almost anything, Angular framework took care for most of the part.

After almost 5 months of development, the project has been done. In general, it was straightforward. Most of the difficulties were related to the responsiveness of UI and debugging, especially for iOS devices. What works on Chrome is not necessarily going to work on Safari. [Here](https://github.com/RemoteDebug/remotedebug-ios-webkit-adapter) there is a lifehack how to make your life easier with remote Safari debugging using Chrome dev tools.

I had a similar project written on C# + UWP, I would say PWA version using TypeScript looks even better and has less code. Angular + PWA is my new favorite combination for desktop/mobile/web apps development. 

Overall, it was an exciting journey and I'm extremely happy with the result!

# Current browsers support

This is not a complete list of PWA features. On the table below I listed only the most important ones.

||Service Workers| Web App Manifest | Push Notifications |
|:------|:------|:------|:------|
| Chrome | yes | yes | yes |
| Edge | yes | no | yes |
| Firefox | yes | no | yes |
| Safari | yes | no | no |

> Web App Manifest is supported only on Chromium-based browsers, but it's not a big deal since you can substitute some of those features using HTML meta tags like it's done on Safari. Push notifications are not supported only on Safari and unfortunately, there is no workaround for this.

# What web apps can do today

To understand your potential with PWA and what you can do and what not, we need to research available web standards and their support by modern browsers.

**What you can:** audio & video capture, advanced camera control, recording media, real-time communication, local notifications, push messages, home screen installation, offline mode, background sync, payments, credentials, bluetooth, USB, offline storage, geolocation, device position & motion, online state, vibration, battery status, speech recognition, clipboard, fullscreen, screen orientation & lock.

**What you can't:** inter-app communication, contacts, SMS, task scheduling, geofencing, proximity sensors, NFC, ambient light, wake lock, virtual & augmented reality, MIME-type handling, web share target.

> By the time of reading this post, the information above can be outdated. To check the current status of web standards supported by your browser go to [What Web Can Do Today](https://whatwebcando.today).

# Performance

You may think that PWA mobile development is only good for small start-ups and rapid prototyping. You can say that it's not possible to achieve the same level of performance as native applications. Yes, you are right, but in most cases with proper optimizations, you can expect more than enough from PWA in terms of speed.

To confirm my words I encourage you to check your phone for the presence of the following applications: **Netflix**, **Instagram**, **Evernote**, **Twitter**, **Amazon**, **Uber**. You might be surprised, but those apps are not native, they are hybrid applications. Hybrid apps run inside a native container and leverage the device’s browser engine to render the HTML and process the JavaScript locally. A web-to-native abstraction layer enables access to device capabilities that are not accessible in mobile web applications. Because hybrid apps consist from HTML and javascript you may expect the same level of performance for PWA.

# Hybrid Applications vs PWA

Since we already touched hybrid application it's worth to compare them with PWA. I would say that PWA is the closest relative for hybrid applications and even its evolution. PWA takes hybrid apps farther.

### Hybrid Applications
* **Require app store** — the only distribution channel for your hybrid application like for native apps is app stores. This limitation means that you need to have developer accounts, pay fees, have a build system for app packages, etc.

* **A limited number of supported platforms** — basically, you can target only those platforms which are supported by your framework like Ionic, PhoneGap, Apache Cordova, etc.

* **Manual version update** — since you distribute the application as a package, to update it you need to create a new version and publish it again to the app store.

* **All features like native apps** — app shell provided by hybrid application becomes a bridge between a system and website and can supply all missing native features to the web app.

### PWA
* **Install from the web** — Progressive Web Applications work on the web and can be installed from there. No app stores required and no packages needed.

* **Require hosting** — in order to a website to serve customers, it requires hosting. The good news here that you can host your PWA as a static website on Github Pages, Azure storage or Amazon S3 with a minimum payment.

* **Auto-version update** — updating PWA as easy as updating the website and all customers will automatically get the fresh version of your application.

* **Works on every platform** — as opposite to hybrid and native applications, you are not tight to a limited number of platforms. If you want, you can use your app in the car or on the fridge, you can do it virtually anywhere where there is a browser.

* **Only features which modern browsers support** — the downside with PWA approach is that not all great features are available in all browsers, especially on Safari. If one of those missing features are critical for you, consider going with a hybrid approach.

# App stores

Ok, we are not living in an ideal world. To cover more audience, you may still want to publish your PWA to app stores even that you don't have to since your application can be installed without them. I have good news for you; you can do it with minimum efforts.

### Google Play Store 
In order to publish PWA to Google Play store, you still need to create a package but don't worry; you don't need to author any Java code. Google has an answer for that with Trusted Web Activities. This feature is available starting Chrome 72.
Trusted Web Activities are a new way to integrate your web-app content such as your Progressive Web Application with your Android app using a protocol based on Custom Tabs.

 The detailed instruction on how to use it, you can find [here](https://developers.google.com/web/updates/2019/02/using-twa).

### Windows Store
There are two options for getting your PWA to Windows Store:

1. If your PWA meets specific criteria, it can be indexed by Bing search engine automatically.

2. Manual submission which is similar to Google Play store, but even simpler, since you don't need to mess with establishing trust between application and website.

Both scenarios fully covered in the following [link](https://docs.microsoft.com/en-us/microsoft-edge/progressive-web-apps/microsoft-store).

### Apple App Store
It's not a secret that Apple is way behind others with web standards support for their browser Safari. PWA app store submission is not straightforward as well. To publish your web app you need to create a hybrid application with web view control to display your website. This is the most difficult solution compare to other stores since it requires writing some code.

### Amazon App Store
If you didn't know, Google Play Store is not the only store where you can publish applications for Android. Another popular store is from Amazon. And it offers the easiest approach, where for [submission](https://developer.amazon.com/app-submission) you need to provide the URL for your Progressive Web Application. No package creation needed!

### Appscope
This is an example of the app store completely dedicated to PWA. [Appscope](https://appsco.pe) is a directory of Progressive Web Apps, showcasing the best PWA examples. All apps listed run entirely in the web browser and launch instantly without an app download. If you wish, they can be saved to your home screen just like any other app. The submission process is straightforward and does not require uploading any packages. 

> If you still think it's too difficult, check [PWABuilder](https://www.pwabuilder.com/) portal where projects for app packages can be created for you for free. PWA Builder was founded by Microsoft as a community guided, open source project to help move PWA adoption forward.

# What’s next?

I believe that PWA is the future of mobile development where will not be any differences between native and web applications. What you can do today with Progressive Web Applications is impressive but the future is even brighter.

Google on Google IO '19 conference revealed some upcoming great features for Chrome browser which will move PWA even further: 
native file system access, unlimited quota, contacts picker, MIME-type handlers, SMS-based authentication, wake lock, web share target and more.
> Some of those features can be already implemented when you read this post.

# Learning resources

* Get started with Progressive Web Apps<br> https://docs.microsoft.com/en-us/microsoft-edge/progressive-web-apps/get-started

* Documentation from Mozilla foundation<br> https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps

* Youtube playlists from Google Chrome Developers community<br> https://bit.ly/2HLaxMU

* Intro to PWA from Google<br> https://developers.google.com/web/progressive-web-apps/

* Workbox - JavaScript Libraries for adding offline support to web apps<br> https://developers.google.com/web/tools/workbox/

* Progressive Web Applications - Start developing today <br>[Presentation](.attachments/6e1601db685248b7bbae55c7c972a11bb7e18b01.pdf)

