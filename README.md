*Current build status:* [![Build Status](https://travis-ci.org/1and1/go-maven-poller.svg)](https://travis-ci.org/1and1/go-maven-poller)

## Go Maven Poller

This is Maven repository package plugin for [Go CD](http://www.go.cd/) and a remix of the [aresok's](https://github.com/aresok/go-maven-poller) codebase. The major differences are:

* Usage of Maven instead of Ant
* Added proxy option to repository configuration
* This plugin parses the *maven-metadata.xml* of a Maven repository instead of using the Nexus API and therefore can be used for a broader range of *Artifactory* and *Nexus* repositories. 
* Use of [GoCD JSON API](http://www.go.cd/documentation/developer/writing_go_plugins/package_material/json_message_based_package_material_extension.html)

Tested on following repositories:

* Maven Central ([Link](https://repo1.maven.org/maven2/))
* JBoss Nexus ([Link](https://repository.jboss.org/nexus/content/repositories/))
* Bintray jCenter ([Link](http://jcenter.bintray.com/))
* Artifactory

### Installation

Download `go-maven-poller.jar` into the `plugins/external` directory of your GoCD server and restart it.

### Repository definition

Repo URL must be a valid http or https URL. Basic authentication (user:password@host/path) is supported.
You may specify a proxy. If your GoCD server system doesn't use the same timezone as the repository, you may set
a specific time zone.
If 'Latest version Tag' is specified, the value of it will be used to determine, if new version is available. It will be not compared to other versions of the package.

![Add a Maven repository][1]

### Package definition

Group Id and Artifact Id refer to the corresponding entries in `pom.xml`. 
Click check package to make sure the plugin understands what you are looking for.
You may set lower and upper version bounds to further narrow down the versions you
want to look for.

![Define a package as material for a pipeline][2]

### Published Environment Variables

The following information is made available as environment variables for tasks:

```
GO_PACKAGE_<REPO-NAME>_<PACKAGE-NAME>_LABEL
GO_REPO_<REPO-NAME>_<PACKAGE-NAME>_REPO_URL
GO_PACKAGE_<REPO-NAME>_<PACKAGE-NAME>_GROUP_ID
GO_PACKAGE_<REPO-NAME>_<PACKAGE-NAME>_ARTIFACT_ID
GO_PACKAGE_<REPO-NAME>_<PACKAGE-NAME>_PACKAGING
GO_PACKAGE_<REPO-NAME>_<PACKAGE-NAME>_LOCATION
```
The LOCATION variable points to a downloadable url.

### Downloading the Package

To download the package locally on the agent, we could write a curl (or wget) task like this:

![Download artifact][3]

```xml
            <exec command="/bin/bash" >
            <arg>-c</arg>
            <arg>curl -o /tmp/mypkg.jar $GO_PACKAGE_REPONAME_PKGNAME_LOCATION</arg>
            </exec>
```

When the task executes on the agent, the environment variables get substituted and the package gets downloaded.
          
### Notes

This plugin will detect at max one package revision per minute (the default interval at which Go materials poll). If multiple versions of a package get published to a repo in the time interval between two polls, Go will only register the latest version in that interval.

[1]: img/add-repo.png  "Define Maven Package Repository"
[2]: img/add-pkgs.png  "Define package as material for a pipeline"
[3]: img/download.png  "Download artifact"