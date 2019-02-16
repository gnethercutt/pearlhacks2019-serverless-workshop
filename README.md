# Serverless: from the ground up to the cloud

Hackathon workshop for Pearlhacks 2019 at UNC Chapel Hill

Learn about the world of serverless computing in this fast paced, bootstrapping workshop. In this session, hackers will learn about Lambda, load balancing, and serverless databases in Amazon Web Services. After this workshop, hackers will have an understanding of how to create backend API's and data tiers without needing to manage servers, operating systems, firewalls, or package managers. Combined with any number of front-end client technologies (HTML5/Javascript, Android, iOS, etc), participants should be able to create fully featured web applications that are highly scalable and super-cheap to run!

- [Cognito] - provides authentication and identity management (read: user logins!)
- [API Gateway] - provides a REST interface for the code you push into the cloud
- [Lambda] - a serverless runtime where your cloud functions run
- [DynamoDB] - a serverless NoSQL database
- [S3] - static content storage; html, css, javascript, etc
- [Amplify] - a command line interface for provisioning all the above AWS resources with minimal effort

## Pre-requisites

- An Amazon Web Services [account](https://aws.amazon.com/free/)
- [NodeJS] 8.x or higher

### Dependency installation

```sh
$ npm install -g @aws-amplify/cli
$ amplify configure
```

## Project creation

```sh
$ npx create-react-app amplify-web-app
$ cd amplify-web-app
$ yarn add aws-amplify aws-amplify-react
$ yarn start
$ amplify init
```

Alternatives to React:
 - [iOS](https://aws-amplify.github.io/docs/ios/start?ref=amplify-iOS-btn)
 - [Android](https://aws-amplify.github.io/docs/android/start?ref=amplify-android-btn)
 - [Pure JavaScript](https://aws-amplify.github.io/docs/js/start?ref=amplify-js-btn&platform=purejs)

## Adding authentication

### Define cloud resources for authentication

```sh
$ amplify add auth
$ amplify push
```

#### Use the amplify components in the skeleton react app: src/App.js 

```javascript
import Amplify from 'aws-amplify'
import config from './aws-exports'
Amplify.configure(config)

import { withAuthenticator } from 'aws-amplify-react'
export default withAuthenticator(App, { includeGreetings: true });
```

## Build your own backend APIs

### Adding a REST api via Lambda

```sh
$ amplify add api
? Please select from one of the below mentioned services REST
? Provide a friendly name for your resource to be used as a label for this category in the project: got
? Provide a path (e.g., /items) /people
? Choose a Lambda source Use a Lambda function already added in the current Amplify project
? Choose the Lambda function to invoke by this path gotpeople
? Restrict API access Yes
? Who should have access? Authenticated users only
? What kind of access do you want for Authenticated users read
? Do you want to add another path? No
```

### Write api logic for your Lambda function, hardcoded at first:

```javascript
app.get('/people', function(rqr, res) {
	const people = [
 	{ name: 'Jon Snow', culture: 'Northmen' },
 	{ name: 'Daenerys Targaryen', culture: "Valyrian"}
 	]
 	res.json({ people });
```

```sh
$ amplify push
```

### GET the resources in App.js, put them into a vanilla HTML table:
```javascript 
import { API } from 'aws-amplify'

state = { people: [] }
async componentDidMount() {
	const data = await API.get('peopleapi', '/people')
	this.setState({ people: data.people })
}
...

   <table>
     <tr><th>Name</th><th>Culture</th></tr>
      {
      this.state.people.map((person, i) => (
        <tr>
	  <td>{ person.name }</td>
          <td>{ person.culture }</td>
	</tr>
      )) 
      }
    </table>
```

### Adding your own NoSQL database and a pre-built CRUD API

```sh
$ amplify add storage
? Please select from one of the below mentioned services NoSQL Database
? Please provide a friendly name for your resource that will be used to label this category in the project: gotparties
? Please provide table name: gotparties

You can now add columns to the table.
? What would you like to name this column: id
? Please choose the data type: string
? Would you like to add another column? No

$ amplify update api
? Please select from one of the below mentioned services REST
? Please select the REST API you would want to update got
? What would you like to do Add another path
? Provide a path (e.g., /items) /parties
? Choose a Lambda source Create a new Lambda function
? Provide a friendly name for your resource to be used as a label for this category in the project: gotparties
? Provide the AWS Lambda function name: gotparties
? Choose the function template that you want to use: CRUD function for Amazon DynamoDB table (Integration with Amazon API Gateway and Amazon Dynam
oDB)
? Choose a DynamoDB data source option Use DynamoDB table configured in the current Amplify project
? Choose from one of the already configured DynamoDB tables gotparties
? Do you want to edit the local lambda function now? Yes

? Press enter to continue 
Succesfully added the Lambda function locally
? Restrict API access Yes
? Who should have access? Authenticated users only
? What kind of access do you want for Authenticated users read/write
? Do you want to add another path? No
Successfully updated resource

$ amplify push
```

### POST to the /parties api in App.js:
```javascript
  post = async() => {
    const response = await API.post('got', '/parties', {
      body: {
        id: JSON.stringify(new Date().getTime()),
        subject: 'Viewing party 2',
        attendees: []
      }
    });
    alert(JSON.stringify(response, null, 2));
  }
  
...  
  
  <button onClick={this.post}>Add viewing party</button>
```

### Update your Lambda functions to use third party services, update the Lambda
(in amplify/backend/function/gotpeople/src/app.js)
```javascript
var axios = require('axios')
app.get('/people', function(req, res) {
  axios.get('https://anapioficeandfire.com/api/characters?page=11&pageSize=15')
    .then(response => {
      const people = response.data
      res.json({ error: null, people })
    })
    .catch(err => {
      console.log('Error fetching characters: ' + err)
      res.json({error: err, people: null })
    })
});
```

```sh
$ ( cd amplify/backend/function/gotpeople/src/; yarn add axios )
$ amplify push
```

## Running 

### Serving your React app locally

```sh
$ yarn start
```

### Hosting your webpages in the cloud

```sh
$ amplify add hosting
$ amplify publish
```

License
----

MIT

[//]: # (These are reference links used in the body of this note)


   [amplify]: <https://aws-amplify.github.io/>
   [cognito]: <https://aws.amazon.com/cognito/>
   [s3]: <https://aws.amazon.com/s3/>
   [API Gateway]: <https://aws.amazon.com/api-gateway/>
   [lambda]: <https://aws.amazon.com/lambda/>
   [DynamoDB]: <https://aws.amazon.com/dynamodb/>
   [nodejs]: <http://nodejs.org>
   [bootstrap]: <http://twitter.github.com/bootstrap/>
   [react]: <https://reactjs.org/>
   [@gnethercutt]: <https://twitter.com/gnethercutt>

   [PlDb]: <https://github.com/joemccann/dillinger/tree/master/plugins/dropbox/README.md>
   [PlGh]: <https://github.com/joemccann/dillinger/tree/master/plugins/github/README.md>
   [PlGd]: <https://github.com/joemccann/dillinger/tree/master/plugins/googledrive/README.md>
   [PlOd]: <https://github.com/joemccann/dillinger/tree/master/plugins/onedrive/README.md>
   [PlMe]: <https://github.com/joemccann/dillinger/tree/master/plugins/medium/README.md>
   [PlGa]: <https://github.com/RahulHP/dillinger/blob/master/plugins/googleanalytics/README.md>
