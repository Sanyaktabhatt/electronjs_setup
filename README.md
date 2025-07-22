## Electron.js Setup Guide

This guide helps you go from ✨ zero to launching your own desktop app ✨ using Electron.js. no stress, just you, Node.js, and desktop magic.


##  Prerequisites

Make sure you’ve got:

- **Node.js**  [Download here](https://nodejs.org/)
- **VSCode** (or any code editor you like)

Check you’re all set:

```bash
node -v
npm -v
```


##  Step-by-Step Setup

### 1️. Open VSCode  
Fire up VSCode and…

### 2️. Create a folder  
Make a new folder for your app (e.g. `MyElectronApp`) and open it in VSCode.

### 3️. Create files  
Inside that folder, add:

- `index.html`
- `index.js`

(You can drop a basic "Hello Desktop" into `index.html` to test it later.)

### 4️. Initialize npm  
Open a terminal **in that folder** and run:

```bash
npm init -y
```

### 5️. Install Electron

```bash
npm install --save-dev electron
```

### 6️. Edit `package.json`  
In the `"scripts"` section, add:

```json
"start": "electron ."
```

So your `"scripts"` should now look like this:

```json
"scripts": {
  "start": "electron ."
}
```

---

##  App Window Setup

### 7️. `index.js` content:

```js
const path = require("path");
const { app, BrowserWindow, ipcMain } = require("electron");
const fs = require("fs");

let win;

function createWindow() {
  win = new BrowserWindow({
    width: 550,
    height: 600,
    webPreferences: {
      nodeIntegration: false,
      contextIsolation: true,
      preload: path.join(__dirname, "preload.js"),
    },
  });

  win.removeMenu();
  win.loadFile("index.html");

  ipcMain.on("load-page", (event, page) => {
    win.loadFile(page);
  });
}

app.whenReady().then(() => {
  createWindow();
  app.on("activate", () => {
    if (BrowserWindow.getAllWindows().length === 0) createWindow();
  });
});
```

### 8️. Add `preload.js`

Create a new file `preload.js` and add:

```js
const { contextBridge, ipcRenderer } = require("electron");

contextBridge.exposeInMainWorld("electronAPI", {
  loadPage: (page) => ipcRenderer.send("load-page", page),
});
```

---

## ▶️ Run the App

### 9️. In your terminal:

```bash
npm start
```

Electron should launch a native desktop window with your HTML file loaded 

---

##  Package Your App (Optional)

Want to turn your app into a downloadable `.exe`, `.dmg`, etc.? Here's how:

### 10. Install Electron Forge

```bash
npm install --save-dev @electron-forge/cli
npm exec --package=@electron-forge/cli -c "electron-forge import"
```

###  Build the App

```bash
npm run make
```

This will generate installable versions of your app inside the `/out` or `/make` folder.

---

## You’re Done!

