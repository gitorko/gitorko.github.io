---
title: VSCode Essentials For Java Developers
date: 2018-07-22 00:00:00
tags: vscode, java
---

We will look at some of the configurations & tips that java developers using vscode for front end and backend development can use.

<!-- more -->

## VSCode

{% asset_img vscode.PNG %}

VSCode is free, open source IDE and with support for Java & Angular Development it checks all the boxes. The below is the user setting file that I use 

```json
{
    "workbench.editor.enablePreview": false,
    "workbench.editor.enablePreviewFromQuickOpen": false,
    "editor.renderWhitespace": "all",
    "editor.insertSpaces": true,
    "java.debug.settings.enableHotCodeReplace": true,
    "editor.rulers": [
        120
    ],
    "window.zoomLevel": 0,
    "editor.tabSize": 2,
    "editor.minimap.enabled": false,
    "editor.detectIndentation": false,
    "checkstyle.configurationFile": "google_checks",
    "checkstyle.version": "8.8",
    "checkstyle.showCheckstyleVersionInvalid": true,
    "checkstyle.autocheck": false,
    "files.exclude": {
      "**/.git": true,
      "**/.gitignore": true,
      "**/build": true,
      "**/.svn": true,
      "**/.hg": true,
      "**/CVS": true,
      "**/.vscode": true,
      "**/.mvn": true,
      "**/.DS_Store": true,
      "**/.settings": true,
      "**/.classpath": true,
      "**/.project": true,
      "**/.sts4-cache": true,
      "**/target": true
    },
    "java.errors.incompleteClasspath.severity": "ignore",
    "git.autofetch": true,
    "editor.wordWrap": "on",
    "editor.multiCursorModifier": "ctrlCmd",
    "java.completion.importOrder": [
      "java",
      "javax",
      "org",
      "com"
    ],
    "java.saveActions.organizeImports": false,
    "editor.formatOnSave": false,
    "java.jdt.ls.vmargs": "-noverify -Xmx1G -XX:+UseG1GC -XX:+UseStringDeduplication -javaagent:\"C:\\Users\\gitorko\\.vscode\\extensions\\GabrielBB.vscode-lombok-0.9.4/server/lombok.jar\" -Xbootclasspath/a:\"C:\\Users\\gitorko\\.vscode\\extensions\\GabrielBB.vscode-lombok-0.9.4/server/lombok.jar\"",
    "workbench.colorTheme": "Default Light+",
    "workbench.iconTheme": "vscode-icons",
    "workbench.startupEditor": "newUntitledFile",
    "gitlens.advanced.messages": {
      "suppressShowKeyBindingsNotice": true,
    },
    "vsicons.projectDetection.autoReload": true
  }
```

One of the things i miss is the ability to run a project similar to Ctrl+F11 in eclipse. There is a new run button introduced but that works only on plain java files. Hence given a maven project how does one run the program?

You need to add org.codehaus.mojo.exec-maven-plugin in your pom.xml

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
Now goto keyboard shortcuts and click on keybindings.json and enter the below config. The args should match the label in task.json. Now click F6 and you have run capability on your IDE. For this to work the build should be successful and java language server should start correctly. Each time you make the change in the java file the language server picks up the changes and compiles it and you should be able to run it immediately.

```json
[
  {
    "key": "f6",
    "command": "workbench.action.tasks.runTask",
    "args" : "run"
  }
]
```

Often times workspace gets corrupted so I delete the storage in %APPDATA%\\Code\\User\\workspaceStorage and restart the IDE to get things back in order.

For formatter setting refer [https://github.com/redhat-developer/vscode-java/wiki/Formatter-settings](https://github.com/redhat-developer/vscode-java/wiki/Formatter-settings)

Hot code replacement works only when the user setting has this entry
```json
"java.debug.settings.enableHotCodeReplace": true,
```

Other plugins recommended are
1. Lombok - Reduce writing boiler plate getter,setter,constructor,toString etc.
2. Annotator - Git blame feature
3. Checkstyle for Java - You can now view the checkstyle errors in the IDE.
4. Code Spell Checker - Spell check.
5. Debugger for Chrome
6. Debugger for Java
7. Docker
8. ESlint
9. GitLens
10. Java Exention Pack
11. Java Test Runner
12. Language Suppor for Java
13. Maven for Java
14. Spring Boot Tools
15. Spring Initializr Java Support
16. vscode-icons
17. Spring boot Application Properties Support

## References
[VSCode] (https://code.visualstudio.com/)
[Java in VSCode](https://code.visualstudio.com/docs/languages/java)

[Java Tutorial with VS Code](https://code.visualstudio.com/docs/java/java-tutorial)
[Spring Boot with VS Code](https://code.visualstudio.com/docs/java/java-spring-boot)
[Java Debugging and Testing](https://code.visualstudio.com/docs/java/java-debugging)
