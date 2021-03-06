---
layout: post
title: WCT build system in Sublime Text
breadcrumb: WCT build system in Sublime Text
---

## WCT build system in Sublime Text

The whole frontend of [ReSnap](http://www.resnap.com) is built on top of Polymer with a lot of self-made Polymer elements. To ensure the quality of the our elements and the application as a whole, we use the [Web Component Tester](https://github.com/Polymer/web-component-tester) (WCT).

During development I try to switch windows as little as possible. To achieve this I use the extensibility of [Sublime Text](https://www.sublimetext.com/). Part of this extensibility is the option to use and define build systems. To make testing my current element as easy as possible I've created a simple build system that executes the unit tests of the currently active test file. It took me a while to get a build system to work and it involved creating a shell script as well.

Following code block is the content of the custom build system:

    {
        "shell_cmd": "./build.sh wct ${file} ${folder}",
        "file_regex": "^(...*?):([0-9]*):?([0-9]*)",
        "selector": "source.html",
        "working_dir": "$folder"
    }

It executes the build.sh script with the current file and folder as input. The shell script contains the following code:

    #!/bin/bash
    # sublime relative paths for builds - v.1
    #########################################
    exectuable=$1
    filePath=$2
    folderPath=$3 # optional
    if [ -a $folderPath ]; then
        # escape special characters
        folderPath=$(echo $folderPath | sed -e 's/\/&]/\\&/g' )
        relativePath=$(echo $filePath | awk -F "$folderPath/" '{print $2}' )
        $exectuable $relativePath
    else
        $exectuable $filePath
    fi

It evaluates the passed parameters to strip the path starting at the root until the project root.

I'm sure that this could be solved more elegantly but it works like a charm in this state. Your suggestions to improve this are more than welcome.
