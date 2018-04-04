---
layout: post
title: Releasing. Or: Keep it as simple as possible
subtitle: ...otherwise, you won't remember
gh-repo: felixreimann/opt4j
gh-badge: [star, fork, follow]
tags: [opt4j, github, gradle, travis]
---

The next step has been made to simplify the release process. Currently, the following manual steps are neccessary:
1 Create a new release on Github (this creates an annotated tag) as described [here](https://github.com/felixreimann/opt4j/wiki/Releasing)
2 Upload the stuff to Maven Central with `./gradlew uploadArchives`

The following steps are automated using [Travis](http://travis-ci.org/) based Continuous Integration for each commit, pull request and tag (see manual step 1):
* The version number, Gradle uses, is computed with `git describe` resulting in the tag name created in step 2. Otherwise, -SNAPSHOT is attached.
* `gradle assemble`s the Java application as a executable binary with all dependencies as jars.
* Code coverage is computed for the different subprojects and a combined root report is generated with `./gradlew jacocoRootReport`.
* For a nice visualization, the code coverage report is sent to
  * [Coveralls](https://coveralls.io/github/felixreimann/opt4j) and, thanks to Fedor,
  * [Codacy](https://app.codacy.com/app/felixreimann/opt4j/dashboard), which directly incorporates [PMD](https://pmd.github.io/) for static code analysis.

As we created a tag in manual step 1, Travis starts the deployment phase (keyword: on tags):
* The files in gradle's `build/distributions`, i.e., the standalone application with all dependent jars are uploaded to Github, which already contains the ziped and tared source files.
* The website, built with `./gradlew website`, includes the links to the new version and is uploaded to Github Pages, using the built-in Travis deployment provider.

This is already quite nice. However, the automation of the Maven Central upload is still missing. Here, we first need to activate the gpg-based signing in Travis. A further step could be to also perform an automatic poke of https://oss.sonatype.org to move the artefacts from staging to Central. However, it seems as Maven does not cope very nicely with the newer web-based publishing schemes and lacks a neat Github integration. Looks a little bit like a "I've been here first" problem. Nonetheless, Maven is at least to my knowldege still the main source for Java jars and, thus, we should support it.

As soon as the CI works as expected (i.e., with as less manual interaction as possible), the configs will be copied to OpenDSE and JReliability, too. Of course, you could ask why as all these projects do not have that many releases. But this is also part of the problem: If you release just once a year, you have to remember or read again about all the stuff you need to do to get a clean release. Thus, I think automation is the key also for smaller projects.
