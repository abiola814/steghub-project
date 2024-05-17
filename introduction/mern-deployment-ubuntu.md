# Deploying a MEAN Stack on AWS

## Introduction

In this project, I will detail how to develop and deploy a MEAN stack application on AWS. This tutorial will guide you through deploying a simple web application with an ExpressJS backend, a MongoDB database layer, and an AngularJS frontend. Users will interact with the AngularJS UI components on the client side, while the backend will be served by an ExpressJS application running on an Ubuntu server on AWS. Data requests will be managed by the NodeJS-based Express server, retrieving data from the MongoDB database and serving it to the frontend.

## Objectives

- Gain hands-on experience with Linux server administration.
- Set up a robust and scalable web server infrastructure.
- Deploy a simple book listing application that allows creating book entries with details such as name, author, ISBN, etc.
- Create a MongoDB database to store and manage the book listings.

## Prerequisites

- AWS account with an Admin user created with full admin permissions. [Getting Started on AWS](https://aws.amazon.com/getting-started/) for a detailed step-by-step guide.
- Install and configure the AWS CLI.
- Create a key pair and a security group.
- Basic knowledge of AWS, Linux, and EC2 instances.
- Basic SSH knowledge.
- Understanding of Database Management Systems, especially the difference between relational and non-relational databases.

## Step 1: Launch a Virtual Server with Ubuntu Server OS

First, create a user data script to install the necessary packages for this project.

```bash
#!/usr/bin/bash

sudo apt update
sudo apt upgrade -y
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
sudo apt install -y nodejs
sudo apt-get install -y net-tools
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
sudo apt update
sudo apt install -y mongodb
sudo service mongodb start
sudo systemctl status mongodb
sudo apt install -y npm
sudo npm install body-parser
```

![index.html image](https://drive.google.com/uc?export=view&id=1W0xDKDIxYTKS2CB533O1Bdvb3vgbv01X)

### Create a Security Group

```bash
aws ec2 create-security-group --group-name meanstackSG --description "Security group for MEAN stack project" --vpc-id <Vpc ID> --tag-specifications 'ResourceType=security-group,Tags=[{Key=Name,Value=meanstackSG}]'
```

### Allow SSH Connection via Port 22

```bash
aws ec2 authorize-security-group-ingress --group-id <Security Group ID> --protocol tcp --port 22 --cidr 0.0.0.0/0
```

### Launch an EC2 Instance

```bash
aws ec2 run-instances --image-id <image id> --count 1 --instance-type t2.micro --key-name <KeyPair> --security-group-ids <Security Group Id> --subnet-id <Subnet Id> --user-data file://Path/userdata.sh --block-device-mappings "[{\"DeviceName\":\"/dev/sdf\",\"Ebs\":{\"VolumeSize\":80,\"DeleteOnTermination\":false}}]" --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=MEAN-Stack}]' 'ResourceType=volume,Tags=[{Key=Name,Value=MEAN-Stack-disk}]'
```

### Connect to the Instance

```bash
ssh -i keypair.pem ubuntu@<public IP address>
```

### Allow HTTP Connection via Port 3300

```bash
aws ec2 authorize-security-group-ingress --group-id "<Security Group ID>" --ip-permissions IpProtocol=tcp,FromPort=3300,ToPort=3300,IpRanges="[{CidrIp=0.0.0.0/0}]"
```

## Step 2: Set Up the Project

Create a project directory and initialize the project.

```bash
mkdir Books && cd Books
npm init --yes
touch server.js
```

Add the following code to `server.js`:

```javascript
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```

## Step 3: Install Express and Set Up Routes

Install the necessary packages and create the project structure.

```bash
sudo npm install express mongoose
mkdir apps && cd apps
touch routes.js
```

Add the following code to `routes.js`:

```javascript
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if (err) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book({
      name: req.body.name,
      isbn: req.body.isbn,
      author: req.body.author,
      pages: req.body.pages
    });
    book.save(function(err, result) {
      if (err) throw err;
      res.json({
        message: "Successfully added book",
        book: result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if (err) throw err;
      res.json({
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendFile(path.join(__dirname + '/public', 'index.html'));
  });
};
```

Create the `models` directory and the `book.js` file:

```bash
mkdir models && cd models
touch book.js
```

Add the following code to `book.js`:

```javascript
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema({
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```

## Step 4: Access the Route with AngularJS

Create the `public` directory and the `script.js` file:

```bash
mkdir public && cd public
touch script.js
```

Add the following code to `script.js`:

```javascript
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http({
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http({
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```

Create the `index.html` file:

```bash
touch index.html
```

Add the following code to `index.html`:

```html
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
```html
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>
        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>
          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```

To start the application, navigate to the `Books` directory and run the server:

```bash
cd Books
node server.js
```

This will start the application, allowing you to access it via a web browser using the HTTP protocol on port 3300.

![index.html image](https://drive.google.com/uc?export=view&id=1mLGamm4Vz5_REoMVeeFPPJXZCBm_DUo0)

![index.html image](https://drive.google.com/uc?export=view&id=1Bm45ZmYrtZ01t-n11dL8YP-sue3eeh4Z)