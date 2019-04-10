---
title: VSCode Essentials For Java Developers
date: 2018-07-22 00:00:00
tags: vscode, java
---

VSCode is free, open source IDE and with support for Java & Angular Development it checks all the boxes needed by an IDE. VSCode provides some great featuers for java development from integration with maven, gradle, checkstyle, code coverage, lombok etc. Here we look at some of the tips and tricks to work with VSCode as IDE.

Github: [https://github.com/gitorko/project61](https://github.com/gitorko/project61)

<!-- more -->

<!-- toc -->

## VSCode

&nbsp;
{% asset_img vscode.PNG %}
&nbsp;

### Feature 1: Use Git Clone & Spring Init in command Pallette

You can use the command palette (Ctrl+Shift+P) to clone repositories, or create new projects using start.spring.io integration.

{% asset_img IMG-01.JPG %}

### Feature 2: Create a new Class using template, you can avoid typing the packagename, class name etc.

{% asset_img IMG-02.JPG %}

### Feature 3: Use language support to avoid typing main: 'public static void main' or sysout: 'System.out.println'

{% asset_img IMG-03.JPG %}

### Feature 4: Hide files you dont wish to view by editing setting.json

{% asset_img IMG-04.JPG %}

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

### Feature 5: If you want to view white spaces

Add this to settings.json

```json
"editor.renderWhitespace": "all",
"editor.insertSpaces": true,
```

### Feature 6: Increase page length to 120

Add this to settings.json

```json
"editor.rulers": [
    120
]
```

### Feature 7: Decide on import order

Add this to settings.json

```json
"java.completion.importOrder": [
        "java",
        "javax",
        "org",
        "com"
    ],
```

Use Right click Source Action->Organize Imports or (Alt+Shift+O)

### Feature 8: To enable hot swap of code on save during debugging

Add this to settings.json, Not required if you are using spring dev tools as spring dev tools support auto reload on save.

```json
"java.debug.settings.enableHotCodeReplace": true,
```

### Feature 8: To enable eclipse formatting

[Formatter settings](https://github.com/redhat-developer/vscode-java/wiki/Formatter-settings)

Use (Ctrl+Shift+I) to format

### Feature 9: Install Lombok plugin for lombok support

[Lombok Annotations Support for VS Code](https://marketplace.visualstudio.com/items?itemName=GabrielBB.vscode-lombok)

### Feature 10: Install licenser to add license info to each java file.

[licenser](https://marketplace.visualstudio.com/items?itemName=ymotongpoo.licenser)

Add this to settings.json

```json
"licenser.customHeader": "Copyright (c) 2019 Company, Inc. All Rights Reserved.",
"licenser.customTermsAndConditions": "",
"licenser.license": "Custom",
"licenser.useSingleLineStyle": false,
```

Use command palette to insert license

{% asset_img IMG-05.JPG %}


### Feature 16: View libraries using the java dependency

{% asset_img IMG-06.JPG %}

### Feature 17: View the git blame feature inline your class file, explore git integration support.

{% asset_img IMG-07.JPG %}

### Feature 17: Explore unit test support & debug unit tests

{% asset_img IMG-08.JPG %}

### Feature 17: Enable checkstyle support & view the inline highlight feature, view the split editor feature

Make the settings change in the workspace instead of global file so that this applies only to the specific project.

[Checkstyle for Java](https://marketplace.visualstudio.com/items?itemName=shengchen.vscode-checkstyle)

{% asset_img IMG-09.JPG %}

[Coverage Gutters](https://marketplace.visualstudio.com/items?itemName=ryanluker.vscode-coverage-gutters)

If the coverage file name is different then change the settings.json 

```json
"coverage-gutters.xmlname": "jacocoTestReport.xml",
```

### Feature 18: Enable Coverage Gutters plugin and view code coverage details inline

You need xml report enabled for this to work, check build.gradle

{% asset_img IMG-10.JPG %}

### Feature 19: Explore debugging & view the variable values

Dock the debugger tool bar.

```json
"debug.toolBarLocation": "docked"
```

{% asset_img IMG-11.JPG %}

### Feature 20: Explore spring boot support for run & application.properties file

Start or debug spring boot application

{% asset_img IMG-12.JPG %}

Get spring property support

{% asset_img IMG-13.JPG %}

### Feature 21: Create shortcut to key bindings to build project

For gradle projects instead of running ./gradlew build each time in terminal you can map it to a task and give a keyboard shortcut.

{% asset_img IMG-14.JPG %}

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

### Feature 23: Taking user input

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

### Feature 24: Run a project

Code lens provides support to run a project by simplying pressing (Ctrl+F5)

### Feature 26: Docker

Build the docker image

{% asset_img IMG-15.JPG %}

Run the docker image

{% asset_img IMG-16.JPG %}

Tag the docker image and push it to public docker hub registry. You need to run docker login before pushing the image.

```bash
docker login
```

{% asset_img IMG-17.JPG %}

### Feature 27: Kubernetes

Deploy to kubernetes cluster

{% asset_img IMG-18.JPG %}

View the deployments

{% asset_img IMG-19.JPG %}

### Feature 28: Short Cuts

Goto Implementation - (Ctrl + F12)
Goto Terminal       - (Ctrl + ~  )

## Features missing

1. Google Java format plugin
2. Static Import
3. Renaming class files

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

1. Checkstyle
2. Coverage Gutters
3. Debugger for Java
4. Dependency Analytics
5. Docker
6. Drools
7. ESlint
8. Git History
9. GitLens
10. Go
11. Gradle Language Support
12. Java Decompiler
13. Java Dependency Viewer
14. Java Extension Pack
15. Java Test Runner
16. Kubernetes
17. Language Support for Java
18. licenser
19. Live Share
20. Lombok
21. markdownlint
22. Maven for Java
23. Python
24. Spring Boot Dashboard
25. Spring Boot Tools
26. Spring Initializer Java Support
27. SQL Server
28. Team Chat
29. TSLint
30. Visual Studio INtelliCode
31. vscode-icons
32. XML Tools
33. YAML

## References

[VSCode](https://code.visualstudio.com/)
[Java in VSCode](https://code.visualstudio.com/docs/languages/java)

[Java Tutorial with VS Code](https://code.visualstudio.com/docs/java/java-tutorial)
[Spring Boot with VS Code](https://code.visualstudio.com/docs/java/java-spring-boot)
[Java Debugging and Testing](https://code.visualstudio.com/docs/java/java-debugging)
