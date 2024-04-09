# puppeteer-config
The best configuration for [PUPPETEER](https://pptr.dev/)

## .puppeteerrc.cjs file

In the main project folder make .puppeteerrc.cjs file
and in puppeteer.config.cjs

```javascript
const {join} = require('path');

/**
 * @type {import("puppeteer").Configuration}
 */
module.exports = {
   cacheDirectory: join(__dirname,'.cache', 'puppeteer'),
};
```

## Usage

### First create this function to get the project path
```javascript
import { dirname } from 'path';
import { fileURLToPath } from 'url';

export const getDir = ()=>{

  let filename;

  var _pst = Error.prepareStackTrace
  Error.prepareStackTrace = function (err, stack) { return stack; };
  try {
      var err = new Error();
      var callerfile;
      var currentfile;

      currentfile = err.stack.shift().getFileName();

      while (err.stack.length) {
          callerfile = err.stack.shift().getFileName();

          if(currentfile !== callerfile) {
              filename = callerfile;
              break;
          }
      }
  } catch (err) {}
  Error.prepareStackTrace = _pst;

  const __filename = fileURLToPath(filename);
  const __dirname = dirname(__filename);
  return __dirname
}
export const appDir = process.cwd();
```

### Use this to launch a browser
```javascript
const folderNmae = uuidv4()
const browser = await puppeteer.launch({
    userDataDir: `./userData/${folderNmae}`,
    headless: "new", args: [
        '--no-sandbox',
        `--user-data-dir=./userData/${folderNmae}`,
        '--disk-cache-dir=./Temp/browser-cache-disk',
        '--aggressive-cache-discard',
        '--disable-cache',
        '--disable-application-cache',
        '--disable-offline-load-stale-cache',
        '--disable-gpu-shader-disk-cache',
        '--media-cache-size=0',
        '--disk-cache-size=0',]
});
```
### At the time of completion of the work
```javascript
await browser.close();

const dirPath = path.join(getDir(), 'userData', folderNmae)

if (fs.existsSync(dirPath)) {
    fs.rm(dirPath,{ recursive: true, force: true }, (err) => {
        if(!err)console.log("Folder Deleted!");
        else console.log(err)
    });
}
```
### Also for auto delete Temp directory use this code
```javascript
const dirPath = path.join(getDir(), 'Temp')

if (fs.existsSync(dirPath)) {
    fs.rm(dirPath, { recursive: true, force: true }, (err) => {
        if (!err) console.log("Temp folder deleted")
        else console.log(err)
    });
}
```

#### For save your logs use this code
```javascript 
import fs from 'fs'
import { appDir } from './dir.js';
import path from 'path';

const newDate = new Date()
const AppRunningTime = `${newDate.getFullYear()}_${newDate.getMonth()}_${newDate.getDate()}_${newDate.getHours()}_${newDate.getMinutes()}_${newDate.getSeconds()}`

if (!fs.existsSync(path.join(appDir,'logs'))) {
  fs.mkdirSync(path.join(appDir,'logs'));
}

fs.mkdirSync(path.join(appDir,'logs',AppRunningTime))

const outputLog = fs.createWriteStream(path.join(appDir,'logs',AppRunningTime,`outputLog.log`));
const errorsLog = fs.createWriteStream(path.join(appDir,'logs',AppRunningTime,`errorsLog.log`));

const consoler = new console.Console(outputLog, errorsLog);

export const consoleWriteToFile = function(){
  console.log = function(txt) {
    const faDate = new Date().toLocaleString("fa")
    consoler.log(faDate ,'\n', txt,'\n\n')
  }
  console.error = function(txt) {
    const faDate = new Date().toLocaleString("fa")
    consoler.error(faDate ,'\n', txt,'\n\n')
  }
};

export default consoler
```
