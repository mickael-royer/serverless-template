![serverless template application logo](https://github.com/mickael-royer/serverless-template/blob/master/img/serverless.png)

A template application composed of a few serverless components. The application has a frontend that displays a catalog of products. The backend relies on a REST API that in turn fetches data from a DynamoDB table. The application is deployable to AWS.

**[Live Preview Here](http://template-b1y1yfjk12.example.com.s3-website-us-east-1.amazonaws.com/index.html)**

## Getting Started

**Note:** Make sure you have Node.js 8+ and npm installed on your machine

1. `npm install --global serverless-components`
2. Setup the environment variables
   * `export AWS_ACCESS_KEY_ID=my_access_key_id`
   * `export AWS_SECRET_ACCESS_KEY=my_secret_access_key`
3. clone this repo to bring down the example code.
  ```sh
  $ git clone https://github.com/mickael-royer/serverless-template.git
  ```
  
## Operations

### Deploy

To deploy the application and create all dependent resources automatically, simply do:

```sh
$ components deploy
```
On `deploy`, the system executes each child component in parallel based on the dependencies:

```
Creating Bucket: 'template-b1y1yfjk12.example.com'
Creating Bucket: 'www.template-b1y1yfjk12.example.com'
Creating Role: func-b1y1yfjk12-he0p09ri-execution-role
Creating Role: func-b1y1yfjk12-foosswwe-execution-role
Creating Role: func-b1y1yfjk12-t3nny7np-execution-role
Created table: 'products-b1y1yfjk12'
Seeding 3 items into table products-b1y1yfjk12.
Creating Lambda: func-b1y1yfjk12-foosswwe
Creating Lambda: func-b1y1yfjk12-t3nny7np
Creating Lambda: func-b1y1yfjk12-he0p09ri
Item inserted to table: 'products-b1y1yfjk12'
{"id":22,"name":"Model A+","description":"A precision-milled, highly durable enhancement of the Model A for performance applications.","price":8.99,"createdAt":"2019-01-30T10:12:11.923Z"}
Item inserted to table: 'products-b1y1yfjk12'
{"id":21,"name":"Model B","description":"A cost-reduced version of our classic offering, providing the highest value.","price":4.99,"createdAt":"2019-01-30T10:12:11.936Z"}
Item inserted to table: 'products-b1y1yfjk12'
{"id":20,"name":"Model A","description":"Our standard, highly reliable part.","price":6.99,"createdAt":"2019-01-30T10:12:11.921Z"}
Setting policy for bucket: 'template-b1y1yfjk12.example.com'
Setting website configuration for Bucket: 'template-b1y1yfjk12.example.com'
Creating CloudFront distribution: 'template-frontend'
Creating Role: api-b1y1yfjk12-5qg6uje1-iam-role-b1y1yfjk12-5qg6uje1
Setting redirection for Bucket: 'www.template-b1y1yfjk12.example.com'
CloudFront distribution 'template-frontend' creation initiated
Set policy and CORS for bucket 'template-b1y1yfjk12.example.com'
Creating API Gateway: "api-b1y1yfjk12-5qg6uje1"
Creating Route53 mapping: 'www.template-b1y1yfjk12.example.com => d2iz6t7vi45gh2.cloudfront.net'
Creating Site: 'template-frontend'
Route53 Hosted Zone 'template-frontend-site-b1y1yfjk12-35qnh7f0-1548843150' creation initiated
Route53 Record Set 'www.template-b1y1yfjk12.example.com => d2iz6t7vi45gh2.cloudfront.net' creation initiated
Syncing files from '/tmp/tmp-57xP1g3axBQdKH' to bucket: 'template-b1y1yfjk12.example.com'
REST API resources:
  POST - https://e563qz3n3e.execute-api.us-east-1.amazonaws.com/dev/products
  GET - https://e563qz3n3e.execute-api.us-east-1.amazonaws.com/dev/products/{id}
  GET - https://e563qz3n3e.execute-api.us-east-1.amazonaws.com/dev/catalog/{...categories}
Static Website resources:
  http://template-b1y1yfjk12.example.com.s3-website-us-east-1.amazonaws.com
```

The `template-app` website can be accessed at the url: `http://template-b1y1yfjk12.example.com.s3-website-us-east-1.amazonaws.com`

### Info

After deployment you can run this command to get access to the static website location and api endpoints:

```sh
$ components info
```

### Remove

To remove the application and delete all dependent resources automatically, simply do:

```sh
$ components remove
```

## The Application Structure

The `retail-app` application has the following structure:

* `serverless.yml`: the configuration file for the application
* `index.js`: the code file for the application
* `code/`: code folder for the `aws-lambda` child component
* `data/products.json`: seed data for products
* `frontend/`: code folder for the `static-website` child component.

### Configuration

The `serverless.yml` is the config file that describes the application and its dependencies. This file is **required**.

The application is composed of the following attributes:

* `type`: a unique identifier that is used to reference the application
* `components`: a set of component dependencies that makes up the application. In turn, each component has the following attributes:
    * `xxxxxx`: a unique name for the instance of the component being used
    * `type`: a unique identifier that is used to reference the component
    * `inputs`: a pre-defined set of input parameters defined by the component. These inputs allow customisation of the component behavior.

### Implementation

The `index.js` file contains any specifc code for the application, above and beyond what the child components provide. This file is **optional**.

The `template-app` seeds the application's DynamoDB table, with some product data using the `insertItem` command exposed by its child component `aws-dynamodb`. The seed data is in the `product.json` file under the `data` folder.

### Components

The `template-app` application is composed of the following components:

* **Web Frontend**: It creates a S3-based static website with the content provided to it. It also uses a Mustache template to display data from the backend. It creates the necessary resources like S3 buckets, policies, CloudFront and Route53 mappings. It returns a url for the website. The `static-website` component encapsulates all that functionality.
* **Lambda functions**: It creates three Lambda functions with the handler code provided to it. It creates default roles and attaches it to the individual Lambda functions. The `aws-lambda` component encapsulates all that functionality.
* **REST API**: It creates a REST API for the AWS API Gateway. It takes a structure for the routes, and maps Lambda functions provided to it. The `rest-api` component encapsulates all that functionality.
* **DynamoDB table**: It creates a DynamoDB table with input parameters like name, keys, indexes, and a model schema. The `aws-dynamodb` component encapsulates all that functionality. See [docs](../../registry/aws-dynamodb/README.md) for details.
