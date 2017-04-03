## Swagger

### YAML Syntax


### codegen

java -jar swagger-codegen-cli-2.2.2.jar generate -i <YAML_file_in> -l nodejs-server -o <dir_out>

 - input file contains the YAML description <YAML_file_in>
 - the target environment eg. nodejs-server
 - output directory where the code must be generated <dir_out>

Once code is generated move to code directory and start the app *npm start*

<http>://localhost:<port>/docs

### Questions

How do we add changes via YAML to an existing modified app ?




### Snippets

```javascript
// from the browser
data = {
  "id": 0,
  "username": 'skaap',
  "firstName": "Steve",
  "lastName": "Swann",
  "email": "swannsg@gmail.com",
  "password": "happyhound",
  "phone": "021724955",
  "userStatus": 0
}
headers = {'Content-Type': 'application/json', Accept: 'application/json'}
url = 'http://localhost:8080/v2/user'

fetch(url, {method:'POST', headers: headers, body:JSON.stringify(data)})
.then(x => console.log(x))
.catch(err => console.log('ERROR', err))
```