---
layout: post
title: "Passing user agent or custom header in React Native WebView"
date: 2019-04-09 10:02:03 +0700
tags: React-Native
---

Using [WebView](https://facebook.github.io/react-native/docs/webview.html) in a React Native application allows us to reuse already built web pages.

We have seen that in most of the "web view" based applications the links in header are mostly turned in native navigational components. It means server should not be sending header if React Native component is asking for web pages. However those headers should be present if the request is not coming from the React Native app.

Passing custom request headers
------------------------------

We can configure React Native app to pass custom request headers when request is made to the server to fetch pages.
```javascript
let customHeaders = {
  "X-DemoApp-Version": "1.1",
  "X-DemoApp-Type": "demo-app-react-native"
}
```
While invoking WebView component we can pass `customHeaders` as shown below.
```javascript
renderWebView() {
  return (
    <WebView
      source={ {uri: this.props.url, headers: customHeaders} }
    />
  )
}
```
Passing user agent
------------------

React Native also allows us to pass “userAgent” as a prop. However it is only supported by android version of React Native.
```javascript
renderWebView() {
  return (
    <WebView
      source={ {uri: this.props.url} }
      userAgent="demo-react-native-app"
    />
  )
}
```
For iOS, we would need to add the following lines to our AppDelegate.m to set the userAgent.
```c
NSString *newAgent = @"demo-react-native-app";
NSDictionary *dictionary = [[NSDictionary alloc] initWithObjectsAndKeys:newAgent, @"UserAgent", nil];
[[NSUserDefaults standardUserDefaults] registerDefaults:dictionary];
```