---
title: 'VSCode Java'
description: 'Java Development in VSCode'
summary: 'VSCode is free, open source IDE. Some of the tips and tricks to work with VSCode for java developers'
date: '2021-05-02'
aliases: [/vscode-java/]
author: 'Arjun Surendra'
categories: [VSCode]
tags: [vscode, checkstyle, openapi, code-coverage, docker, kubernetes, hot-swap]
toc: true
---

VSCode is free, open source IDE. We look at some of the tips and tricks to work with VSCode for java developers

Github: [https://github.com/gitorko/project61](https://github.com/gitorko/project61)

## VSCode

![](vscode.png)

### Feature 1: Explore Git Clone & Spring Init commands

You can use the command palette (Ctrl+Shift+P) to clone repositories, or create new projects using start.spring.io integration.

![](image1.1.png)

![](image1.2.png)

### Feature 2: Explore Java language support

Use language support to avoid typing main: 'public static void main' or sysout: 'System.out.println'

![](image2.1.png)

![](image2.2.png)

![](image2.3.png)

### Feature 3: Explore the Gradle Exentison

View the gradle tasks

[Gradle Extension Pack](https://marketplace.visualstudio.com/items?itemName=richardwillis.vscode-gradle-extension-pack)

![](image3.1.png)

### Feature 4: Hide files you dont wish to view

![](image4.1.png)

Add this to settings.json

```json
"files.exclude": {
  "**/.classpath": true,
  "**/.DS_Store": true,
  "**/.factorypath": true,
  "**/.git": true,
  "**/.gitignore": true,
  "**/.gradle": true,
  "**/.hg": true,
  "**/.mvn": true,
  "**/.project": true,
  "**/.settings": true,
  "**/.sts4-cache": true,
  "**/.svn": true,
  "**/.vscode": true,
  "**/.idea": true,
  "**/out": true,
  "**/bin": true,
  "**/build": true,
  "**/CVS": true,
  "**/gradle": true,
  "**/target": true,
  "**/.attach_pid*": true,
  "**/logs": true
}
```

### Feature 5: Replace tabs with white spaces

Add this to settings.json

```json
"editor.renderWhitespace": "all",
"editor.insertSpaces": true,
```

### Feature 6: Increase page length to 120

Add this to settings.json to specify line length.

```json
"editor.rulers": [
    120
]
```

### Feature 7: Decide on import order

Add this to settings.json to specify the import order.

```json
"java.completion.importOrder": [
        "java",
        "javax",
        "org",
        "com"
    ],
```

Use Right click Source Action->Organize Imports or (Alt+Shift+O)

### Feature 8: Enable eclipse formatting

Enable specific formatter.

[Formatter settings](https://github.com/redhat-developer/vscode-java/wiki/Formatter-settings)

[Formatter xml](https://github.com/gitorko/project61/blob/main/config/formatter/dev_code-style_formatter.xml)

```bash
    "java.format.settings.url" :"file:///Users/home/dev_code-style_formatter.xml",
```

Use (Ctrl+Shift+I) to format

### Feature 9: Install Lombok plugin

Avoid writing boilerplate code with lombok.

[Lombok Annotations Support for VS Code](https://marketplace.visualstudio.com/items?itemName=GabrielBB.vscode-lombok)

### Feature 10: Add license info to each file

This will add a license header to the file.

[licenser](https://marketplace.visualstudio.com/items?itemName=ymotongpoo.licenser)

Add this to settings.json

```json
"licenser.customHeader": "Copyright (c) 2021 Company, Inc. All Rights Reserved.",
"licenser.customTermsAndConditions": "",
"licenser.license": "Custom",
"licenser.useSingleLineStyle": false,
"licenser.author": "Company",
```

Use command palette to insert license

![](image10.1.png)


### Feature 11: Explore java dependency tree

View the dependency tree of the project.

![](image11.1.png)

### Feature 12: Explore Git

Check git blame inline and view git commits. View the git graph to visualize the tree. 

![](image12.1.png)

![](image12.2.png)

### Feature 13: Explore unit test support & debug unit tests

Run the unit tests and view the report.

![](image13.1.png)

![](image13.2.png)

### Feature 14: Explore checkstyle support

View the inline highlight feature. Make the settings change in the workspace instead of global user settings file so that this applies only to the specific project.

[Checkstyle for Java](https://marketplace.visualstudio.com/items?itemName=shengchen.vscode-checkstyle)

```json
"java.checkstyle.configuration": "${workspaceFolder}/config/checkstyle/checkstyle.xml"
```

![](image14.1.png)

### Feature 15: Explore inline code coverage

You need xml report enabled for this to work, check build.gradle, after the build the jacocoTestReport.xml is generated that is read by the coverage extension to highlight lines of code not covered by unit tests.

[Coverage Gutters](https://marketplace.visualstudio.com/items?itemName=ryanluker.vscode-coverage-gutters)

If the coverage file name is different then change the settings.json

```json
"coverage-gutters.xmlname": "jacocoTestReport.xml",
```

![](image15.1.png)

### Feature 16: Explore Debugging and Hot Code Replacement/Hot Swap

Dock the debugger tool bar.

```json
"debug.toolBarLocation": "docked"
```

```bash
curl --location --request GET 'http://localhost:9090/api/age/10-10-2020' --header 'Content-Type: application/json'
```

![](image16.1.png)

To enable hot code replace set the following properties, for spring boot projects with dev tools the reload is automatic, if dev tools is not present in the project then you can use Hot code replacement (HCR), which doesn’t require a restart, is a fast debugging technique in which the Java debugger transmits new class files over the debugging channel to the JVM. Make sure 'java.autobuild.enabled' is enabled.

```json
"java.debug.settings.hotCodeReplace": "auto",
"java.autobuild.enabled" : true
```

![](image16.2.png)

### Feature 17: Explore Spring Boot Support

Start or debug spring boot application

![](image17.1.png)

Get spring property support

![](image17.2.png)

### Feature 18: Create shortcut to key bindings to build project

For gradle projects instead of running ./gradlew build each time in terminal you can map it to a task and give a keyboard shortcut.

![](image18.1.png)

Add this to the tasks.json, everytime you run a task called 'run' it will build the project.

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "type": "shell",
            "command": "./gradlew clean build",
            "group": "none"
        }
    ]
}
```

Now lets create a shortcut goto "Keyboard Shortcuts" and click on '{}' icon.  Add this to keybindings.json, now press F6 to build the project

```json
[
    {
        "key": "f6",
        "command": "workbench.action.tasks.runTask",
        "args" : "build"
      }
]
```

### Feature 19: Reading user input

To take user input from command line you need to change shell type in launch.json config to integratedTerminal

```json
{
  "type": "java",
  "name": "CodeLens (Launch) - Main",
  "request": "launch",
  "mainClass": "com.demo.project61.Application",
  "projectName": "project61",
  "console": "integratedTerminal"
}
```

### Feature 20: Explore Docker

Build the docker image

![](image20.1.png)

Run the docker image

![](image20.2.png)

Tag the docker image and push it to public docker hub registry. You need to run docker login before pushing the image.

```bash
docker login
```

Push the image

![](image20.3.png)

### Feature 21: Explore Kubernetes

Deploy to kubernetes cluster

![](image21.1.png)

View the deployments

![](image22.2.png)

### Feature 22: Explore Rest Client

Explore Reset client Thunder Client

![](image21.1.png)

![](image21.2.png)

### Feature 23: Sync the settings

Link the accounts to sync the settings

![](image23.1.png)

### Feature 24: Connect to DB and query

Query a database

![](image24.1.png)

![](image24.2.png)

### Feature 25: Use live share

Share your workspace

![](image25.1.png)

### Feature 26: Explore Open API

Create Open API spec file and test it

![](image26.1.png)

![](image26.2.png)

### Feature 27: Shortcuts

Goto Implementation - (Ctrl + F12)
Goto Terminal       - (Ctrl + ~  )
Quick Fix           - (Ctrl + .  )

## Problems

1. Often times workspace gets corrupted so I delete the storage in %APPDATA%\\Code\\User\\workspaceStorage and restart the IDE to get things back in order. [Clean the workspace directory](https://github.com/redhat-developer/vscode-java/wiki/Troubleshooting#clean-the-workspace-directory)

    Windows : %APPDATA%\Code[ - Variant]\User\workspaceStorage\
    MacOS : $HOME/Library/Application Support/Code[ - Variant]/User/workspaceStorage/
    Linux : $HOME/.config/Code[ - Variant]/User/workspaceStorage/

2. Another problem often seen is when multiple project exist on workspace but if one of them fails to build then all the projects in the workspace wont work. So for now keep one workspace to one project mapping.

### Maven Execution

If you need to execute maven project from command line, You need to add org.codehaus.mojo.exec-maven-plugin in your pom.xml

```xml
<plugin>
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>exec-maven-plugin</artifactId>
  <version>1.6.0</version>
</plugin>
```

Then configure task by 'Ctrl+Shift+P' then 'Tasks: Configure task' and select the project. Edit the tasks.json

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "run",
      "type": "shell",
      "command": "mvn exec:java  '-Dexec.mainClass=com.myproject.Main'",
      "group": "none"
    }
  ]
}
```

## Plugins recommended

To export the plugins you use

```bash
code --list-extensions > extensions.list
```

To install all plugins at one time

```bash
cat extensions.list |% { code --install-extension $_}
```

extensions.list

```
42Crunch.vscode-openapi
Angular.ng-template
CoenraadS.bracket-pair-colorizer-2
DavidAnson.vscode-markdownlint
dbaeumer.vscode-eslint
DotJoshJohnson.xml
eamodio.gitlens
eg2.vscode-npm-script
GabrielBB.vscode-lombok
golang.go
hashicorp.terraform
humao.rest-client
jim-moody.drools
johnpapa.vscode-peacock
mhutchie.git-graph
ms-azuretools.vscode-docker
ms-kubernetes-tools.vscode-kubernetes-tools
ms-ossdata.vscode-postgresql
ms-python.python
ms-python.vscode-pylance
ms-toolsai.jupyter
ms-vscode-remote.remote-containers
ms-vscode-remote.remote-ssh
ms-vscode-remote.remote-ssh-edit
ms-vscode-remote.remote-ssh-explorer
ms-vscode-remote.remote-wsl
ms-vscode-remote.vscode-remote-extensionpack
ms-vscode.js-debug-nightly
ms-vscode.vscode-typescript-next
ms-vscode.vscode-typescript-tslint-plugin
ms-vsliveshare.vsliveshare
msjsdiag.debugger-for-chrome
msjsdiag.vscode-react-native
mtxr.sqltools
mtxr.sqltools-driver-pg
naco-siren.gradle-language
Pivotal.vscode-spring-boot
PKief.material-icon-theme
rangav.vscode-thunder-client
redhat.java
redhat.vscode-commons
redhat.vscode-xml
redhat.vscode-yaml
richardwillis.vscode-gradle
richardwillis.vscode-gradle-extension-pack
richardwillis.vscode-spotless-gradle
ryanluker.vscode-coverage-gutters
shengchen.vscode-checkstyle
VisualStudioExptTeam.vscodeintellicode
vscjava.vscode-java-debug
vscjava.vscode-java-dependency
vscjava.vscode-java-pack
vscjava.vscode-java-test
vscjava.vscode-maven
vscjava.vscode-spring-boot-dashboard
vscjava.vscode-spring-initializr
vscode-icons-team.vscode-icons
xabikos.JavaScriptSnippets
ymotongpoo.licenser
zhuangtongfa.material-theme
```

## References

[VSCode](https://code.visualstudio.com/)

[Java in VSCode](https://code.visualstudio.com/docs/languages/java)

[Java Tutorial with VS Code](https://code.visualstudio.com/docs/java/java-tutorial)

[Spring Boot with VS Code](https://code.visualstudio.com/docs/java/java-spring-boot)

[Java Debugging and Testing](https://code.visualstudio.com/docs/java/java-debugging)
