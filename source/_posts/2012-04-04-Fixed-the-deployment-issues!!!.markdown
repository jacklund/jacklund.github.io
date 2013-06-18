---
date: 2012-04-04
title: Fixed the deployment issues!!!
layout: post
comments: true
---
I finally figured out how to get Ant + Ivy to copy artifacts from one Artifactory repository to another, modifying the appropriate metadata (AKA "promotion"). First, you need a separate ivy.xml file (I call mine "ivy-publish.xml") that looks like this:

```xml
    <ivy-module version="2.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:noNamespaceSchemaLocation=
             "http://ant.apache.org/ivy/schemas/ivy.xsd">
        <info organisation="foo" module="FooDeploy" status="integration" />
        
        <configurations>
        	<conf name="integration"/>
        	<conf name="milestone"/>
        	<conf name="release"/>
        </configurations>
    
    	<publications>
    		<artifact name="Foo" ext="war" />
    		<artifact name="Foo-src" ext="jar" />
    	</publications>
    
    	<dependencies>
    		<dependency org="foo" name="Foo" rev="latest.integration"
              conf="integration->deploy" transitive="false" />
    		<dependency org="foo" name="Foo" rev="latest.milestone"
              conf="milestone->deploy" transitive="false" />
    		<dependency org="foo" name="Foo" rev="latest.release"
              conf="release->deploy" transitive="false" />
    	</dependencies>
    </ivy-module>
```

And then, you need an ant target that looks like this:

```xml
	<target name="publish-stage">
		<ivy:configure file="${common.build.dir}/ivysettings.xml"
      		host="myserver" realm="Artifactory Realm" username="user"
      		passwd="password!" override="true" />
		<ivy:retrieve file="ivy-publish.xml" conf="integration"
      		pattern="${ivy.lib.dir}/[conf]/[artifact].[ext]"
        	    ivypattern="${ivy.lib.dir}/[conf]/ivy.xml"/>
		<ivy:buildnumber organisation="${ivy.organisation}"
      		module="${ivy.module}" revision="1.0" defaultBuildNumber="1"
      		resolver="stage" />
		<ivy:publish resolver="stage"
      		pubrevision="1.0.${ivy.new.build.number}"
      		status="milestone" overwrite="true" update="true">
			<artifacts pattern="${lib}/integration/[artifact].[ext]" />
		</ivy:publish>
	</target>
```

What this does is first retrieve the "dependencies" (the artifacts we're interested in) specified in the ivy-publish.xml file to lib/integration. Then, it turns around and publishes them, changing the status from "integration" to "milestone".

Works like a charm.
