# MyStaticWeb
**Creating S3 bucket on aws for static website:**
1. Create S3 bucket "my-example-s3bucket" on aws with "Block all public access" unchecked (using AWS S3 console)
2. Once created, go to "my-example-s3bucket" and then choose properties
3. Edit "Static website hosting", select "enable" under "Static website hosting" and add "index.html" as a default page in the box under "Index document"  and save changes
4. Go to "permissions",then edit "Bucket Policy" and add the following policy in the Bucket policy and save changes:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::my-example-s3bucket/index.html"
            ]
        }
    ]
}
```
***Creating IAM roles for github to access S3:**
1. Under IAM services Access management, select Users>add users
2. Provide username "S3-access-role" and select "Access key - Programmatic access"
3. On next screen select "AmazonS3FullAccess" on "Attach existing policies directly"
4. Add relevant tags and Create user
5.  Download csv file with "Access key ID" and "Secret access key" to be used for Github


***Creating Github repository**
1. Create public repository "MyStaticWeb" with README file
2. Inside repo "MyStaticWeb", go to Settings >Sectets>Actions  and select "New repository secret" to add 2 secrets: "Access key ID" and "Secret access key" which were downloaded from AWS in previous step
3. Now clone this repository using gitbash on desktop and follow the steps below:
```
git init
mkdir .github
mkdir .github/workflows
touch .github/workflows/main.yml
vi .github/workflows/main.yml
```
----main.yml------------------
```
name: Upload Website

on:
  push:
    branches:
    - master

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Set AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-region: 'ap-southeast-2'
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}

    - name: copy to S3
      run: aws s3 sync . s3://my-example-s3bucket --exclude '.git*/*'
(:wq)---------------------------------

touch index.html
vi index.html
```
----------index.html----------
```
<html xmlns="http://www.w3.org/1999/xhtml" >
<head>
    <title>My Website Home Page</title>
</head>
<body>
  <h1>This is chandan Pokhrel's website</h1>

</body>
</html>
--------------------------------
```
```
git add index.html
git add .github/
git commit -m "first commit"
git branch -M master
git remote add origin https://github.com/crspo/MyStaticWeb.git
git push -u origin master
```
**NOTE: please change the bucket name and update 'master' to 'main' or relevant branch in main.yml as per your repo**
