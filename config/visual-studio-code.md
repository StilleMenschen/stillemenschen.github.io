# Visual Studio Code

settings.json

```json
{
  "files.autoSave": "onFocusChange",
  "update.mode": "start",
  "window.restoreWindows": "none",
  "editor.renderWhitespace": "all",
  "emmet.triggerExpansionOnTab": true,
  "php.validate.executablePath": "D:\\php\\php.exe",
  "php.executablePath": "D:\\php\\php.exe",
  "prettier.printWidth": 120,
  "javascript.format.insertSpaceBeforeFunctionParenthesis": true,
  "typescript.format.insertSpaceBeforeFunctionParenthesis": true,
  "eslint.options": {
    "env": {
      "es2022": true
    },
    "parserOptions": {
      "ecmaVersion": 2022,
      "sourceType": "module",
      "ecmaFeatures": {
        "impliedStrict": true,
        "jsx": true
      }
    },
    "rules": {
      "semi": "off"
    }
  },
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[markdown]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[jsonc]": {
    "editor.defaultFormatter": "vscode.json-language-features"
  },
  "[html]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "rest-client.decodeEscapedUnicodeCharacters": true,
  "editor.suggestSelection": "first",
  "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
  "workbench.iconTheme": "material-icon-theme",
  "files.autoGuessEncoding": true,
  "python.languageServer": "Pylance",
  "editor.linkedEditing": true,
  "python.analysis.completeFunctionParens": true,
  "editor.codeActionsOnSave": {
    "source.fixAll": "explicit"
  },
  "path-intellisense.mappings": {
    "@": "${workspaceRoot}\\src"
  },
  "files.trimTrailingWhitespace": true,
  "git.autofetch": "all",
  "python.autoComplete.extraPaths": ["D:\\Python311\\Lib\\site-packages"],
  "python.analysis.extraPaths": ["D:\\Python311\\Lib\\site-packages"],
  "terminal.integrated.defaultProfile.windows": "PowerShell",
  "security.workspace.trust.enabled": false,
  "editor.fontFamily": "Consolas, 'Microsoft Yahei', 'Courier New', monospace",
  "gitlens.hovers.avatars": false,
  "gitlens.views.commits.avatars": false,
  "gitlens.views.repositories.avatars": false,
  "gitlens.views.fileHistory.avatars": false,
  "gitlens.views.branches.avatars": false,
  "gitlens.views.remotes.avatars": false,
  "gitlens.views.tags.avatars": false,
  "gitlens.views.contributors.avatars": false,
  "gitlens.views.searchAndCompare.avatars": false,
  "gitlens.blame.avatars": false,
  "gitlens.views.searchAndCompare.files.layout": "tree",
  "files.eol": "\n",
  "hexeditor.columnWidth": 16,
  "hexeditor.showDecodedText": true,
  "hexeditor.defaultEndianness": "little",
  "hexeditor.inspectorType": "aside",
  "extensions.ignoreRecommendations": true,
  "editor.unicodeHighlight.includeStrings": false,
  "[python]": {
    "editor.formatOnType": true,
    "editor.defaultFormatter": "ms-python.autopep8",
    "editor.formatOnSave": true
  },
  "rest-client.timeoutinmilliseconds": 6000,
  "terminal.integrated.persistentSessionReviveProcess": "onExitAndWindowClose",
  "[dockercompose]": {
    "editor.defaultFormatter": "ms-azuretools.vscode-docker"
  },
  "cSpell.userWords": ["pageable", "vuex"],
  "[vue]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "conventionalCommits.scopes": ["kubernetes", "java", "javascript", "spring", "algorithm", "python", "sql"],
  "[javascriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "git.ignoreRebaseWarning": true,
  "workbench.colorTheme": "Monokai",
  "window.commandCenter": false,
  "window.newWindowDimensions": "inherit",
  "autopep8.args": ["--max-line-length=256"]
}
```

keybindings.json

```json
[
  {
    "key": "alt+d",
    "command": "editor.action.deleteLines",
    "when": "textInputFocus && !editorReadonly"
  },
  {
    "key": "ctrl+shift+k",
    "command": "-editor.action.deleteLines",
    "when": "textInputFocus && !editorReadonly"
  },
  {
    "key": "ctrl+k",
    "command": "deleteAllRight",
    "when": "terminalEditorFocus || terminalFocus"
  }
]
```

Last Modified 2024-05-05
