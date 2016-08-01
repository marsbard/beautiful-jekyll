---
comments: true

layout: post
title: How to point maven to an alternative artifact provider
subtitle: Use your own custom artifactory or nexus server
bigimg: /img/tropicbeach.jpg
---

This is probably in a hundred other places on the net, but I find myself using it again and again and
referring to my other servers already set up to find out how to do it, so I figured I'd store it here
in case of an emergency (zombies ate my PCs or something).

Create a file in your maven folder, usually `$HOME/.m2` called `settings.xml` and populate it with the 
following XML. Change occurrences of `[[USER]]`, `[[PASSWORD]]`, and `[[URLPREFIX]]` to suitable values 
for your setup. 

Note that with artifactory you may optionally use an encrypted password in this file. To find your 
encrypted password go to your artifactory profile, enter your password and you will then be able to see 
your encrypted password. Either the unencrypted or encrypted password will work. 

```
<settings xmlns="http://maven.apache.org/SETTINGS/1.1.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.1.0 http://maven.apache.org/xsd/settings-1.1.0.xsd">
	<servers>
		<server>
			<username>[[USER]]</username>
			<password>[[PASSWORD]]
			</password>
			<id>central</id>
		</server>
		<server>
			<username>[[USER]]</username>
			<password>[[PASSWORD]]
			</password>
			<id>snapshots</id>
		</server>
	</servers>
	<profiles>
		<profile>
			<repositories>
				<repository>
					<snapshots>
						<enabled>false</enabled>
					</snapshots>
					<id>central</id>
					<name>libs-release</name>
					<url>[[URLPREFIX]]/libs-release</url>
				</repository>
				<repository>
					<snapshots />
					<id>snapshots</id>
					<name>libs-snapshot</name>
					<url>[[URLPREFIX]]/libs-snapshot</url>
				</repository>
			</repositories>
			<pluginRepositories>
				<pluginRepository>
					<snapshots>
						<enabled>false</enabled>
					</snapshots>
					<id>central</id>
					<name>plugins-release</name>
					<url>[[URLPREFIX]]/plugins-release</url>
				</pluginRepository>
				<pluginRepository>
					<snapshots />
					<id>snapshots</id>
					<name>plugins-snapshot</name>
					<url>
						[[URLPREFIX]]/plugins-snapshot
					</url>
				</pluginRepository>
			</pluginRepositories>
			<id>artifactory</id>
		</profile>
	</profiles>
	<activeProfiles>
		<activeProfile>artifactory</activeProfile>
	</activeProfiles>
</settings>


```
