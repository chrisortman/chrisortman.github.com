---
published: true
---

#Release builds with TFS + GIT

###Background 

The most common usage of TFS that I've seen is to have a main TFS branch (_current/main_) and release branches for versions.

So developers work off the _current_ branch eventually code is ready to advance to QA and is _branched_ to v3.4 (arbitrary number)

There would then be a TFS Build definition for each major version so in this case there are build definitions for 

* v3.4
* v3.3
* current

Each time you _branch_ you create a new build definition.

You tell the build definition which branch it should pull sources from as well as what version numbers it should use. Typically specifying something that yields 3.4.<BuildNumber>

This works about as well as anything based on TFS source control can. 

###Problem

So now you want to use GIT with your TFS Builds 

My first instinct based on previous workings with TFS was to want to mimic my existing structure, have TFS build master and then generate a tag using whatever build number it has generated. But....

Whenever I use git I always want a tag v3.4.3 (please use [semantic versioning](http://semver.org) ) that is annotated with release notes.

    git tag -a v3.4.3
    #type in release notes probably pulled from git log with previous version
    
 But that means the tagging has to happen on the developer machine.
 
Already I have a bit of a problem. Now throw in the desire to produce versioned nuget packages and it's worse.

###Solution 

TFS seems to have support for building from GIT source control based on a _something that refers to a commit_ so maybe I can use that to my advantage.

Since release type builds are always requested manually I can make the tag to build from a parameter of the build. 

Instead of creating a build per version I would just create a Release-AppName build definition and then specify the tag to build from. 

Not I just need to make sure that that variable is available to my MSBuild scripts and I can generate the correct version for my assembly info files and my nuget package.