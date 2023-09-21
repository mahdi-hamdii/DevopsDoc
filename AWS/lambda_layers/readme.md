# Create a Lambda Containing SecretsManager
```
$ mkdir nodejs
```
Once you have the `nodejs` directory created, initialize `npm`
```
$ cd nodejs
$ npm init
```
Once npm is initialized, install the required node_modules
```
$ npm install --save aws-sdk
```
Once the installation is complete we wil have something like this:
```
├── nodejs
      └── package.json
      └── node_modules
              └── aws-sdk
              └── node_module2
```
- now add the `SecretsManager.js` file and zip the nodejs folder.

- Create a layer containing the .zip file generated. 

- In the lambda function, add the following code to get the newly created lambda:
```python
import { createRequire } from "module";
const require = createRequire(import.meta.url);

const SecretsManager = require('/opt/nodejs/SecretsManager');
# lambda code
export const.handler = async(event) => {
    var secret = await SecretsManager.getSecret(secretName, region);
    secret =  JSON.parse(secret)
}
```