+++
title = 'Android Continuous Integration and Delivery — Next level Android development'
author = 'Amit Bhandari'
date = 2018-07-09T12:00:30+05:30
draft = false
tags = ['Android', 'Continuous Integration', 'Deployment']
description = 'Everything you need to know about Android Continuous Integration and Deployment.'
+++

Android Continuous Integration and Delivery, Everyone is talking about it these days. 
So what exactly it is and why do you need it? Well following article can explain better than me.

[Continuous integration, explained](https://aws.amazon.com/devops/continuous-integration/)

If you are working on Android project with team of 2 or more and pushing and integrating code on daily basis on VCS, you must use Android Continuous Integration to make your life easier with all those build fails, error and basic test executions.
Every time someone pushes code, Android Continuous Integration will build and run unit tests automatically on server and notify you of any errors instantly (it will notify you for successful build as well). 
How cool is that?

As a Android developer, most boring thing in world for me is signing apk, going to play console and uploading it. 
And again there is this task of uploading deobfuscation file of trace logs. 
Thanks to google, they exposed google play console API for automating that stuff too. 
Well, wouldn’t it be awesome if you just merge your code in your master, and your Android Continuous Integration server uploads signed apk and deobfuscation file to your play console for you? Off course it will be.

If you want to set up this arrangement for your Android project, 
go on reading the article as I would try to mention all the steps necessary to set up the system, 
but if I do miss something, do not threaten to kill my family, just ask nicely and I would help you out.

*P.S. This process is not as simple as double click on installer.exe -> next -> next -> finish -> launch, it’s highly likely you may run into shit load of problems (or you may not), 
but don’t give up and after successful setup, you will thank yourself for life. 
It took me almost a day to set this up as I was very new to CI thing. 
If you face any issue, you can always drop comment below and I will try to help you out.*

--- 

## Android Continuous Integration (CI)

Jenkins is the name which pops up in head of most people when they hear the phrase CI, it is most used, 
Java based, open source automation server. But I personally found Team City by JetBrains 
(yes, same awesome people who came up with Kotlin and Android Studio) much easier to configure and use. 
Even though Team City is proprietary software with commercial license, 
they provide Freemium license up to 100 build configurations (which is more than enough for Hobbyist like me). 
Without wasting any time, let’s get started installing team city in your server. 
(It can be installed in your computer as well if you are the only person working on project)


### Installation on Linux
For other OS, refer official [installation](https://confluence.jetbrains.com/display/TCD10/Installation+Quick+Start) quick start guide

[Download](https://www.jetbrains.com/teamcity/download/) latest version in tar.gz from official website.
Open up terminal, change directory to downloads and issue simple ‘tar xfz TeamCity<version number>.tar.gz’ to unzip the content in same directory.
To start Team City server, move to TeamCity/bin directory and give ‘runAll.sh start’ command. To stop, give ‘runAll.sh stop’. You could create alias in your bashrc file or mention command in startup program list to automatically start TC server on startup.
By default team city runs on 8111 port, so open up a browser and browse to http://localhost:8111/ to set up build configurations. Follow [this](https://confluence.jetbrains.com/display/TCD10/Installation+Quick+Start#InstallationQuickStart-StartTeamCityfortheFirstTime) for first time set up and account creation.

Now create your first project on Team City by clicking on Administration on top right corner. 
There are several options provided to create project i.e. from Github, Bitbucket, Repository URL or manual setup. 
You could setup Github connection in your TC by providing credentials and TC will list all your repositories to choose from.

![regular](ci/1_ci.webp#center)

Select repository you want to setup CI for and click on Proceed.

![regular](ci/2_ci.webp#center)


TC will try to auto read build steps for project by going through project files and find the correct one most of the times, 
if not, you can always provide the build steps manually

![regular](ci/3_ci.webp#center)


Select **Gradle** and click on use selected. (later we will modify build steps to include auto upload of signed apk to play console)

By default, TC will take master branch of repository as default one for setting up VCS trigger for starting build. 
TC will periodically check for any changes on repository and initiate build as soon as new code is pushed to it. 
You can customize almost everything in project settings, like default branch, repository checking interval etc.

I personally like to keep 3 branches in my android project on Git i.e. master (release build), beta (alpha/beta build) and development (where I commit crap on hourly basis). 
I set up my VCS trigger on ‘beta’ branch so that whenever I push any changes (or merge) to ‘beta’, automatic build and deployment will happen in TC. 
**And I don’t even have to manually upload release.apk and mapping.txt on console.**

To edit default branch of your project (change VCS trigger to beta branch in my case), 
go to **Project Settings -> VCS Roots -> Edit (on particular VCS root) -> Default branch** and update its value.

![regular](ci/4_ci.webp#center)

Android Continuous Integration Setup on Team City

![regular](ci/5_ci.webp#center)

And your TC is setup to check beta branch every 1 minute and checkout changes (if any) and build the project. 
You can setup notification system to notify you in case of failure (or success as well) via email. 
Explore the thing in your TC settings.

--- 

## Continuous Delivery (CD)

Now comes the exciting part, automatic upload of release apk to alpha/beta/production and mapping.txt to De-obfuscation files in your play console. 
Google provides play console API for doing this and fortunately (as always), some good people came together to write a [library](https://github.com/Triple-T/gradle-play-publisher) to make it super easy for us to use it in our project without dealing with APIs our self. 
(If you just had Thumbs Up and want to do something TOOFANI, go ahead and consume those APIs for yourself)

Before getting started with [Gradle Play Publisher](https://github.com/Triple-T/gradle-play-publisher), make sure you provide proper signing info in your gradle file so that it can automatically sign your apk with your key and initiate upload.
There are multiple ways you could mention signing info in your gradle file, one being mentioning passwords and key file path directly in gradle file itself, 
but its extremely insecure and could expose your passwords to anyone having access to code.

### Signing info in Gradle file

Best way is to write those details in external file, list that file in your .gitignore and read values from that file in your build.gradle.
Create new file at root directory of project and name it keystore.properties and enter this 5 entries in it(with your values). 
Don’t worry about p12 file yet, I will tell you how to get it shortly.

{{< highlight properties >}}
storeFile=/key/file/path.jks
keyAlias=key0
storePassword=notasecret
keyPassword=notasecret
p12keyFile=/path/to/your/p12key/file/Google Play Android Developer-2355232355.p12
{{< /highlight >}}

And reference this file and values in build.gradle like this.

{{< highlight groovy >}}

// Load keystore
def keystorePropertiesFile = rootProject.file("/path/to/your/keystore.properties")
def keystoreProperties = new Properties()
keystoreProperties.load(new FileInputStream(keystorePropertiesFile))

android{
    signingConfigs{
        release {
            storeFile file(keystoreProperties['storeFile'])
            storePassword keystoreProperties['storePassword']
            keyAlias keystoreProperties['keyAlias']
            keyPassword keystoreProperties['keyPassword']
        }
    }
    buildTypes {
        release {
            signingConfig signingConfigs.release
            ... 
        }
    }
}
{{< /highlight >}}

Source for this [info](https://stackoverflow.com/questions/20562189/sign-apk-without-putting-keystore-info-in-build-gradle/33218300#33218300)
To make sure your signing info is correct and works, choose ‘release’ build variant from ‘Build Variants’ tab on lower left side of Android Studio (if you are using Eclipse, seriously?) and try installing app on device. If no error, good to go. If errors, google is always there to help. (before rushing to google by copying error blindly, read it. Most of the times, error arises because of our stupid mistake)

### Enabling play console API access and getting p12 key file
To get started using play console API, we need p12 key file and instructions to link your project and creating service account is nicely documented by google [here](https://developers.google.com/android-publisher/getting_started).

Main steps are

1. Going to API access page in developer console and linking your project by clicking on link.
![regular](ci/6_ci.webp#center)
2. We need to set up API access client by creating service account. 
You will see option to create service account on the same page below under ‘Service Accounts’ section.
![regular](ci/7_ci.webp#center)
3. Click on ‘grant access’ and give required permissions.
![regular](ci/8_ci.webp#center)

Once your service account is set up, go to [Google API Console](https://console.developers.google.com/), open up credentials from left navigation, click on **create credentials -> Service account key -> Choose your service account -> Choose P12 and click create.** 
This will download P12 key file in your browser. **Do not share this file with anyone.**

![regular](ci/9_ci.webp#center)

Once you successfully got key file, place it somewhere in your server securely (make sure you don’t include the file in VCS) and mention that path in keystore.properties file. 
Now mention your service account email and apk upload track (alpha, beta or prod) in build.gradle like this. 
(Refer [documentation](https://github.com/Triple-T/gradle-play-publisher) for all the options and how to use them)

{{< highlight groovy >}}
play {
    track = 'alpha'
    serviceAccountEmail = 'ab-***t@api-********************-54989.iam.gserviceaccount.com'
    pk12File = file(keystoreProperties['p12keyFile'])
}
{{< /highlight >}}

And sync your project with gradle. To make sure signing info is correctly set up and your build is ready to upload apk to console upon successful build, go to terminal and change working directory to your root android project and give command *‘./gradlew tasks’* and you should be able to see all the available play store tasks 
(you will not see ‘publishApkRelease’ if there is problem in your signing info in build.gradle)

![regular](ci/10_ci.webp#center)

You can test your apk upload task by running ‘./gradlew publishApkRelease’ in terminal right away. 
It should invoke build and upload release.apk along with the de-obfuscation to your play console.

### Setting up TC to upload your apk
I keep my repository organized in 3 branches as discussed earlier. 
I have set beta branch of my code to invoke build and publishApkRelease on Android Continuous Integration TC server. 
You could set up your system as per your requirement and liking. 
All you have to do is mention ‘publishApkRelease’ task in your build steps in TC Project Settings like below.

![regular](ci/11_ci.webp#center)

Now you can test your Android Continuous Integration setup by pushing test changes on branch (‘beta’ in my case). 
**I hope you don’t face any error which google won’t be able to help with.**

---

This article was originally written on medium [here](https://medium.com/@amit-bhandari/android-continuous-integration-and-delivery-next-level-android-development-cfc67405a56b)

---