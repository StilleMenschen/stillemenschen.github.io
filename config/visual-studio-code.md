# Visual Studio Code

settings.json

```json
{
    "files.autoSave": "onFocusChange",
    "update.mode": "start",
    "window.restoreWindows": "none",
    "editor.renderWhitespace": "all",
    "emmet.triggerExpansionOnTab": true,
    "python.formatting.provider": "yapf",
    "python.linting.pylintPath": "pylint",
    "python.pythonPath": "python.exe",
    "php.validate.executablePath": "php.exe",
    "php.executablePath": "php.exe",
    "prettier.printWidth": 120,
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
        "sz.tystnad.tech": "linux",
        "192.168.55.80": "linux",
        "192.168.52.64": "linux"
    },
    "terminal.integrated.profiles.windows": {
        "Git-Bash": {
            "path": "bash.exe",
            "args": [
                "--login"
            ],
            "icon": "terminal-bash"
        },
    },
    "terminal.integrated.defaultProfile.windows": "Git-Bash",
    "security.workspace.trust.enabled": false,
    "editor.fontFamily": "Consolas, 'Microsoft Yahei', 'Courier New', monospace",
    "workbench.colorTheme": "One Dark Pro Darker",
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
    "bracket-pair-colorizer-2.depreciation-notice": false
}
```

Last Modified 2021-12-29
