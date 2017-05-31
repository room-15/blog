---
layout: post
title: How to publish your libraries to JCenter()
post_author: Eric Cugota
---

Searching on the internet how to publish a library into jcenter() can put one into trouble. 
One may even find himself trapped into a spider web of gradle scripts, build configs and ./gradlew commands.
fear not, CptEric has a solution to this.

### Setting up Bintray

To get your api key you must be registered on [bintray](https://bintray.com).

You can find it on your user panel (click in your name, click on edit, click on api key row).

After that  you have to create a new maven repository (package name, image, description, github link, favourite coffee brand... )

### Configuring your library

First of all, you must have project with a library module setup.

Inside the library module’s (from now on, lib) build.gradle, you must add this just under apply plugin line :

```groovy
ext {
   PUBLISH_GROUP_ID = 'com.yourpackagename'
   PUBLISH_ARTIFACT_ID = 'yourmodulename'
   PUBLISH_VERSION = '0.0.2'
}
```

 - Group id : your package name.
 - Artifact id : the thing you’re building, it has to match the module library name.
 - Publish version : no comment required, your current module version.

This , when uploaded, will allow you to put compile `'com.cpteric:ericutils2:0.0.2'` (as an example) in your gradle files.

On the bottom of the file, you must put this line

```groovy
apply from: 'https://raw.githubusercontent.com/blundell/release-android-library/master/android-release-aar.gradle'
```

Other tutorials will make you run a lot of clutter code and build a lot of methods in gradle, but that apply from does it automatically.
The only thing that's not cool about this approach is that you have to manually upload the zips.
But it's something i can live with if i don't have to create my own gradle upload script.

Now, go to your project build.gradle file and add this on dependencies : 

```groovy
dependencies {
	classpath 'com.android.tools.build:gradle:1.2.3'
	classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.2'
	classpath 'com.github.dcendents:android-maven-plugin:1.2'
}
```

This makes the prior apply from work.

Now on your `local.properties` file you must add this:

```groovy
bintray.user=YOUR BINTRAY USERNAME
bintray.apikey=YOUR BINTRAY API KEY
```

Once done, on the android studio terminal run  the next sequence :

```groovy
./gradlew clean build generateRelease
```

It will generate a zip.

### Publishing to Jcentral()

On your maven repository, click add new version, add the name you want to, add the number you want to, click accept, & enter into the version page. 

Inside the general tab , on the right side, click add files via GUI, click on upload, add the generated zip, _and pick the checkbox that says explode on upload._

Click send, and after that, go to your package and click on the “link to jcenter” (bottom right). 

Fill the questionary, and it takes between some minutes up to 3 days to be accepted into the jcenter repository. 

After that, you can find you package via `compile 'com.cpteric:ericutils2:0.0.2'` or whatever you picked as `'package : Artifact : version'`.


#### About the author

*Eric Cugota is an Android and ios developer for [UseIT Easy Interfaces](http://www.useit.es/) since 2014, has three foundation degrees on software development and computer hardware, and is currently stuying a 3D, animation , posproduction and videogame creation one. gotta catch 'em all.*
