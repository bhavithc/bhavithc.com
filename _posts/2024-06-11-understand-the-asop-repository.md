---
layout: post
title: Understand the asop repository
date: '2024-06-11 21:50:02 +0530'
tags: [aosp]
categories: [aosp]
---
When I first time cloned and build the aosp code, I was very much wonder how few commands can get us the android OS 

### Commands

#### Clone the repo 
```
$ repo init --partial-clone -b main -u https://android.googlesource.com/platform/manifest
$ repo sync -c -j8
```

#### Build the code 
```
$ source build/envsetup.sh
$ lunch aosp_cf_x86_64_phone-trunk_staging-userdebug
$ make
```

Android project is very big, the way the build systems are designed is pretty much amazing. just by executing the above commands with lot of patience, good computer and internet with in couple of hours say (~6Hrs) we get android os ready

Since I was new to repo tool, I was really fed up understanding the whole big picture. So here where I share my insights on how this aosp build works 

Entire android repositories are maintained under some thing called **Google Git** which is similar to github.com where linux source is hosted. 

Google Git: https://android.googlesource.com/

My first impression was that the UI of google git is very very bad to be frank. By looking at the UI itself we can't able to understand what to clone and where to clone and which repo to clone. That job is taken care by repo tool, thanks to it

I copied the content of Google Git to text file and executed `cat google_git.txt | wc -l` and found out that there are **2789** repositories present in **Google git** among them we need to start at some point, That is where repo tool clones

Here where our aosp first repo entry point exist 
1. The first entry point aosp build is `https://android.googlesource.com/platform/manifest/` 
![alt text](/assets/aosp_images/manifest.png)
2. Click on more under tags in the above image now you will be under `https://android.googlesource.com/platform/manifest/+refs`
3. Choose your favorite build number (tag) in my case I chose `android-14.0.0_r22` -> click on it now end up in 
   `https://android.googlesource.com/platform/manifest/+/refs/heads/android-14.0.0_r22` 
![alt text](/assets/aosp_images/android-14.0.0_r22.png)
4. This is where it contains a secrete file called `default.xml` which contain all further repo link to clone. Which you can think of like a submodules in git
5. This file is copied to manifest folder under .repo folder then further clone will happen using this `default.xml`

We know that is a very huge repo to clone and build, so we can add our own manifest, then we can add or remove the repo which is not required for us 

For details refer raspberry pi aosp manifest file
https://github.com/raspberry-vanilla/android_local_manifest/tree/android-14.0.0_r22

> Note: Above notes are purely with my understanding, any thing which is not correct please pardon me and add a comments I gonna correct it

