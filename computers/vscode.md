# Visual Studio Code
### Alex D. Hill
#### 28 JAN 2025

#### VSCode
VSCode is a integrated development environment (IDE) that allows you to write code with processes such as 'linting' and 'debugging' built in. One of the benefits of VSCode is the versatility it has, and bein able to work in multiple languages, installing packages you need for how you work.

#### Extensions
The packages I reference above are called 'extensions' in VSCode. Extensions are installed through a tab on the left side of the window that lets you easily search and install new packages. Some of the most common extensions are:
- Python
- R
- Markdown
- Remote SSH

#### Connecting to the servers
VSCode would be alost worthless to us if we couldn't use it to edit our projects on the GI servers. To do this we need to install the `Remote SSH` extension which adds a little button to the bottom left of your window which when pressed allows you to select `Connect window to remote server`. The first time you do this, you need to enter the ssh command you would normally use to connect to the server, which is something along the lines of `ssh username@emerald`. Once you have done this, you can select the server you want to connect to from the list that appears.

When on the server, we need to edit our VSCode settings to ignore certain file directories (specifically work and node_modules) as VSCode can use a lot of computing power tracking useless files. There is a guide on the GI Wiki on how to make this edit.

#### Setting up Python and R
I have specialized R and Python environments set up on the servers, and mamba is certainly the recommended way to manage the programming environments. In order for VSCode to access the correct installs, I provide the paths to R and python in terms of the `$CONDA_PREFIX` variable that is set by your `.bashrc` file.

#### Recommended settings
```
{
    "workbench.startupEditor": "none",
    "workbench.editorAssociations": {
        "*.copilotmd": "vscode.markdown.preview.editor",
        "*.pdf": "pdf.preview"
    },
    "editor.renderWhitespace": "all",
    "editor.minimap.enabled": false,
    "editor.fontFamily": "'Source Code Pro', Menlo, Monaco, 'Courier New', monospace",
    "editor.fontLigatures": true,
    "editor.inlineSuggest.enabled": true,
    "editor.rulers": [
        120,
    ],
    "files.exclude": {
        "**/$*": true,
        "desktop.ini": true,
        "**/*.tmp": true,
        "**/node_modules": true,
        "**/work": true
    },
    "github.copilot.editor.enableAutoCompletions": true,
    "jupyter.interactiveWindow.textEditor.executeSelection": true,
    "r.bracketedPaste": true,
    "r.plot.defaults.colorTheme": "vscode",
    "r.removeLeadingComments": true,
    "r.rterm.linux": "$CONDA_PREFIX/r/bin/radian",
    "r.lsp.diagnostics": false,
    "r.alwaysUseActiveTerminal": true,
    "r.lsp.debug": true,
    "r.rpath.linux": "$CONDA_PREFIX/r/bin/R",
    "r.lsp.promptToInstall": false,
    "r.plot.useHttpgd": true,
    "r.plot.devArgs": {
        "width": 2000,
        "height": 2000
    },
    "[latex]": {
        "editor.wordWrap": "on",
        "editor.defaultFormatter": "James-Yu.latex-workshop"
    },
}
```