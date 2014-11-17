---
layout: post
title: Blogging Like a Hacker
published: true
---

One of the great things about SharePoint 2010 and 2013 is that it's easy to start building HTML+JS based applications without very much overhead. There are scenarios where it may be overkill to develop an SharePoint app model based application or times where you want to simply get going proofing out or prototyping an app that may or may not eventually be turned into an app-model based app. Fortunately, getting started with a SPA in SharePoint is as simple as creating a document library, dropping some files in using your editor of choice and starting to code away.

By creating a SPA that lives in a document library, you can directly hit SharePoint services without needing to go through the cross-domain library, or bundle your app within a container.

[[TODO: Flesh this out]]

I'd like to mention that the choice of MV* framework that powers your SPA isn't that important for these tips and tricks. While your preference may be AngularJS or Ember, perhaps you're a fan of react or Knockout, or you're a roots kinda guy and like to build everything up from scratch, the important part is that the end goal is to build business application functionality for your end user, not to be dogmatic on a particular framework. The discussion of if a particular MV* best aligns with your customer, the lifecycle of the app, your team and their knowledge and the goals of your business is a discussion to be had elsewhere -- these tips and tricks apply irrespective of your choice of framework.

Organize your Files Carefully
It's really easy to create a clutter of html/css/js files across a number of document libraries. Always do your do diligence as a developer and organize your files within folders and subfolders, organizing your files by function.

A typical structure within a document library may look like this:

[[TODO: screenshot ]]

Find a organization that works for you and your team and stick to it, deleting unnecessary files and cruft when you don't need them. I can't stress this enough!

Create a mini-CDN within a document library 
If you're worried about leaking requests to files on 3rd party CDNs (and you should be), when including JavaScript libraries, create a root-level location where your common javascript libraries will be referenced -- these may be 3rd party libraries such as your framework libs, SPServices, a common set of javascript files used across multiple aps.

Some examples of location names might be
[[TODO: Screenshot]]
\lib
\components
\vendor

Ensure Blob Caching is enabled and functioning
Blob Caching enables SharePoint to retrieve your html/js/css files directly from the file system, rather than going out to the database on each request. It's important that if you're in an premises environment, that your blob caching is working normally. In an Office 365 scenario, you can hopefully disregard this tip as it should have been configured for you.

To test to see if blob caching is working in your environment [[TODO: Instructions]]

Use a rich Html/JavaScript/CSS editor
One of the most useful things built into SharePoint is the ability to view any document library within Explorer View. Under-the-scenes, this is powered by a protocol known as WebDAV, but you already knew that. What makes this useful is that by opening a document library in Explorer View, you can then use your html/javascript/css editor of choice. So if your preference is Visual Studio, WebStorm, Sublime Text, or NotePad++ you can edit your documents in relative ease by just opening files in explorer view via your editor of choice.

By leveraging an editor that at the minimum performs syntax-highlighting, and hopefully does more, you can reduce common JS and HTML issues while developing your SPA.

Bypassing Strict File Handling
Starting with SharePoint 2010, .HTML and other file types can only be opened in the browser when permissive file handling is enabled. In strict mode, .html files have a content-disposition header of 'attachment' tacked onto them which instructs the browser to download them rather than view them. Changing from Strict mode to Permissive mode is a web-application scoped policy, so you're going to have to beg your SharePoint administrator to enable it on any web apps that you wish to view .html files on Unfortunately, on Office 365, this just plain cannot be done.

Fortunately, there's a better way. If you simply rename your .html files to .aspx -- they will render in the browser and no additional configuration is required.

Even if you can change to permissive file handling, there's unfortunately some SharePoint introduced side effects of placing .html files in a document library -- SharePoint tries to be too clever by half and attempts to ensure that all .html files in a doclib have a root html tag and will inject a office meta tag in the file in the process. This will cause any of your .html files that are meant to be partial or partial-view files to often mess up your SPA.

Eliminate pain by using a page viewer web part when including your SPA on a branded page

SharePoint Developers have been used to dropping a content editor web part in whenever they've need to jQuerify existing SharePoint content for years now. However, if you need to have your SPA 'hosted' page that assumes the branding of the rest of the site, I recommend to use the page viewer web part in lieu of the content editor web part. There are a number of reasons for this.

Since the Content Editor web part renders content in the context of the site's master page, you'll be at the mercy of whatever markup/scripts that have been included in the master page. The master page may be maintained by a different team which may evolve over time unbeknownst to you. I've seen cases where the master page actually contains invalid/broken HTML, references a version of a required library that's several versions (read: years) behind, conflicting style classes and so forth. All of these will break your SPA or cause it to be really difficult to debug what's going on.

Further, as the content editor web part injects the contents of the referenced file (or inline text) right into the page, files referenced by the content editor web part must not contain a root html or body tag, as this will cause the owning page to break. This limits your SPA when you need to reference style sheets, script files, or declare meta or doctype tags.

Lastly, since SharePoint renders everything with a root form, basic html form validation starts to break down. You often will not be able to use traditional html form validation techniques, or other input fields on the page outside of your app will conflict with your validation. Often times the web part page cannot be saved since the form doesn't validate (which the web part pane respects).

By using a page viewer web part and pointing it to your SPA's index.html aspx file, you can eliminate all these potential sources of headache. You also get the benefit of just visiting the .aspx page directly and testing it outside of nesting the SPA within a branded web part page.

TL;DR; Develop your SPA as an isolated SPA, and then put it in a web part container, vs trying to develop a SPA in a content editor web part.


Respect traditional HTML/JS tips and techniques
Just because you're building an html+js app under SharePoint doesn't preclude you from observing typical techniques for creating html -- use doctype declarations, proper meta tags, include script references at the bottom of your body, and so forth.

Tools such as ySlow and page speed can help with this and provide suggestions.

It's also highly beneficial to use minification tools to help reduce and combine your javascript files. 

Using Bower and Grunt with Explorer View
[[TODO: Flesh this out]]


Using these tips and tricks will smooth the development of SPAs in SharePoint and allow for your end users to be able to use applications that live in SharePoint that aren't limited by the traditional confines of SharePoint application development.