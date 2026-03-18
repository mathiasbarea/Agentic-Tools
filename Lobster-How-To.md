
# How to Use Lobster

Welcome to this quick guide on how to install and set up Lobster. This document covers the general installation process, along with specific troubleshooting steps and configuration fixes required for Windows users.

<br>  

## 🗂️ Table of Contents

- [Getting Started](#-getting-started)
- [Windows Compatibility Fixes](#-windows-compatibility-fixes)
- [OpenClaw Configuration](#-openclaw-configuration)

<br>


## 🚀 Getting Started


To begin using Lobster, you need to clone the repository, install its dependencies, and link it so it can be executed globally from your CLI.

*  **[Official Lobster Repository](https://github.com/openclaw/lobster):** The main source code for the project.

  
Run the following commands sequentially in your terminal:

  

```bash
git  clone [https://github.com/openclaw/lobster](https://github.com/openclaw/lobster)
cd  lobster
pnpm  install
pnpm  build
npm  link
lobster  --help
```
  <br>
 

## 🪟 Windows Compatibility Fixes

  

If  you  are  running  this  on  a  Windows  operating  system (as of  March  17,  2026), you will likely encounter execution errors such as `UNSUPPORTED_ESM_URL_SCHEME`  or  build  failures  related  to  the  `rm  -rf dist`  command.

  

To  resolve  these  issues,  specific  modifications  must  be  made  to  the  `package.json`  and  `lobster.js`  files.

  

-  **[Windows  Fix  Implementation](https://github.com/openclaw/lobster/compare/main...mathiasbarea:lobster:fix/windows-compatibility):**  You  can  view  the  exact  changes  I  made  to  resolve  these  OS-specific  issues  in  this  commit  comparison.

<br>
  
## ⚙️ OpenClaw Configuration

  

Finally,  you  need  to  configure  your  `openclaw.json`  file  to  allow  the  agent  to  interact  with  Lobster  and  enable  the  necessary  plugins.

  

**1. Allow the Lobster executable:** You must add `lobster`  to  the  `alsoAllow`  array  so  the  system  permits  its  execution.

  
```JSON
"alsoAllow": [
  "lobster",
  "llm-task"
]  
```

**2. Enable the required plugin:** Ensure that the `llm-task` is set to `true` within your plugin entries.

```JSON
"plugins": {
  "entries": {
    "llm-task": {
      "enabled": true
    }
  }
}
```