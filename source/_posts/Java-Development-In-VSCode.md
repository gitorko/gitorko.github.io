---
title: Java Development in VSCode
date: 2021-05-01 00:00:00
tags: 
- vscode
- java
- checkstyle
- open api
- code coverage
- docker
- kubernetes
- hot swap
categories:
- [VSCode]
---

VSCode is free, open source IDE. Here we look at some of the tips and tricks to work with VSCode.

Github: [https://github.com/gitorko/project61](https://github.com/gitorko/project61)

<!-- more -->

<!-- toc -->

## VSCode

{% asset_img vscode.PNG %}

### Feature 1: Explore Git Clone & Spring Init commands

You can use the command palette (Ctrl+Shift+P) to clone repositories, or create new projects using start.spring.io integration.

{% asset_img image1.1.PNG %}

{% asset_img image1.2.PNG %}

### Feature 2: Explore Java language support

Use language support to avoid typing main: 'public static void main' or sysout: 'System.out.println'

{% asset_img image2.1.PNG %}

{% asset_img image2.2.PNG %}

{% asset_img image2.3.PNG %}

### Feature 3: Explore the Gradle Exentison

View the gradle tasks

[Gradle Extension Pack](https://marketplace.visualstudio.com/items?itemName=richardwillis.vscode-gradle-extension-pack)

{% asset_img image3.1.PNG %}

### Feature 4: Hide files you dont wish to view

{% asset_img image4.1.PNG %}

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

[Formatter xml](https://github.com/gitorko/project61/blob/master/config/formatter/dev_code-style_formatter.xml)

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

{% asset_img image10.1.PNG %}


### Feature 11: Explore java dependency tree

View the dependency tree of the project.

{% asset_img image11.1.PNG %}

### Feature 12: Explore Git

Check git blame inline and view git commits. View the git graph to visualize the tree. 

{% asset_img image12.1.PNG %}

{% asset_img image12.2.PNG %}

### Feature 13: Explore unit test support & debug unit tests

Run the unit tests and view the report.

{% asset_img image13.1.PNG %}

{% asset_img image13.2.PNG %}

### Feature 14: Explore checkstyle support

View the inline highlight feature. Make the settings change in the workspace instead of global user settings file so that this applies only to the specific project.

[Checkstyle for Java](https://marketplace.visualstudio.com/items?itemName=shengchen.vscode-checkstyle)

```json
"java.checkstyle.configuration": "${workspaceFolder}/config/checkstyle/checkstyle.xml"
```

{% asset_img image14.1.PNG %}

### Feature 15: Explore inline code coverage

You need xml report enabled for this to work, check build.gradle, after the build the jacocoTestReport.xml is generated that is read by the coverage extension to highlight lines of code not covered by unit tests.

[Coverage Gutters](https://marketplace.visualstudio.com/items?itemName=ryanluker.vscode-coverage-gutters)

If the coverage file name is different then change the settings.json

```json
"coverage-gutters.xmlname": "jacocoTestReport.xml",
```

{% asset_img image15.1.PNG %}

### Feature 16: Explore Debugging and Hot Code Replacement/Hot Swap

Dock the debugger tool bar.

```json
"debug.toolBarLocation": "docked"
```

```bash
curl --location --request GET 'http://localhost:9090/api/age/10-10-2020' --header 'Content-Type: application/json'
```

{% asset_img image16.1.PNG %}

To enable hot code replace set the following properties, for spring boot projects with dev tools the reload is automatic, if dev tools is not present in the project then you can use Hot code replacement (HCR), which doesn’t require a restart, is a fast debugging technique in which the Java debugger transmits new class files over the debugging channel to the JVM. Make sure 'java.autobuild.enabled' is enabled.

```json
"java.debug.settings.hotCodeReplace": "auto",
"java.autobuild.enabled" : true
```

{% asset_img image16.2.PNG %}

### Feature 17: Explore Spring Boot Support

Start or debug spring boot application

{% asset_img image17.1.PNG %}

Get spring property support

{% asset_img image17.2.PNG %}

### Feature 18: Create shortcut to key bindings to build project

For gradle projects instead of running ./gradlew build each time in terminal you can map it to a task and give a keyboard shortcut.

{% asset_img image18.1.PNG %}

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

{% asset_img image20.1.PNG %}

Run the docker image

{% asset_img image20.2.PNG %}

Tag the docker image and push it to public docker hub registry. You need to run docker login before pushing the image.

```bash
docker login
```

Push the image

{% asset_img image20.3.JPG %}

### Feature 21: Explore Kubernetes

Deploy to kubernetes cluster

{% asset_img image21.1.JPG %}

View the deployments

{% asset_img image22.2.JPG %}

### Feature 22: Explore Rest Client

Explore Reset client Thunder Client

{% asset_img image21.1.PNG %}

{% asset_img image21.2.PNG %}

### Feature 23: Sync the settings

Link the accounts to sync the settings

{% asset_img image23.1.PNG %}

### Feature 24: Connect to DB and query

Query a database

{% asset_img image24.1.PNG %}

{% asset_img image24.2.PNG %}

### Feature 25: Use live share

Share your workspace

{% asset_img image25.1.PNG %}

### Feature 26: Explore Open API

Create Open API spec file and test it

{% asset_img image26.1.PNG %}

{% asset_img image26.2.PNG %}

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

```json
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
