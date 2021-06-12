# vscode

settings.json

```json
{
    "files.autoSave": "onFocusChange",
    "update.mode": "start",
    "window.restoreWindows": "none",
    "editor.renderWhitespace": "all",
    "emmet.triggerExpansionOnTab": true,
    "python.autoComplete.addBrackets": true,
    "python.formatting.provider": "yapf",
    "python.formatting.yapfPath": "yapf.exe",
    "python.linting.pylintPath": "pylint.exe",
    "python.pythonPath": "python.exe",
    "php.validate.executablePath": "php.exe",
    "php.executablePath": "php.exe",
    "prettier.printWidth": 120,
    "prettier.proseWrap": "always",
    "javascript.format.insertSpaceBeforeFunctionParenthesis": true,
    "typescript.format.insertSpaceBeforeFunctionParenthesis": true,
    "vetur.format.defaultFormatter.html": "js-beautify-html",
    "vetur.format.defaultFormatterOptions": {
        "js-beautify-html": {
            "wrap_attributes": "preserve-aligned",
            "indent_size": 4
        },
        "prettier": {
            "singleQuote": true
        }
    },
    "eslint.options": {
        "env": {
            "es2019": true
        },
        "parserOptions": {
            "ecmaVersion": 2020,
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
    "[jsonc]": {
        "editor.defaultFormatter": "vscode.json-language-features"
    },
    "[html]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[vue]": {
        "editor.defaultFormatter": "octref.vetur"
    },
    "rest-client.decodeEscapedUnicodeCharacters": true,
    "editor.suggestSelection": "first",
    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
    "workbench.iconTheme": "material-icon-theme",
    "files.autoGuessEncoding": true,
    "python.languageServer": "Pylance",
    "python.showStartPage": false,
    "editor.linkedEditing": true,
    "python.analysis.completeFunctionParens": true,
    "editor.codeActionsOnSave": {
        "source.fixAll": true
    },
    "workbench.editorAssociations": {
        "*.ipynb": "jupyter.notebook.ipynb"
    },
    "editor.wordBasedSuggestionsMode": "allDocuments",
    "path-intellisense.mappings": {
        "@": "${workspaceRoot}/src"
    },
    "files.trimTrailingWhitespace": true,
    "git.autofetch": "all",
    "remote.SSH.remotePlatform": {
        "192.168.0.1": "linux"
    },
    "workbench.colorTheme": "Remedy - Dark (Straight)",
    "workbench.startupEditor": "gettingStarted",
    "terminal.integrated.profiles.windows": {
        "Git-Bash": {
            "path": "bash.exe",
            "args": [
                "--login"
            ],
            "icon": "terminal-bash"
        },
    },
    "terminal.integrated.defaultProfile.windows": "Git-Bash"
}
```

Last Modified 2021-03-13
