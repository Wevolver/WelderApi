---
title: Welder API Reference
language_tabs:
  - python: Python
  - shell : Curl
---

# Introduction

Welcome to the Welder API! Using Welder you'll be able to interact with a git backend,
either self-hosted or local, to create and manipulate project repositories.

```
API Endpoint
http://localhost:8000

Summary of Resources:
/username/projectname/create
/username/projectname/delete
/username/projectname/readfile?path=
/username/projectname/newfolder
/username/projectname/listbom
/username/projectname/archive/download
/username/projectname/upload
/username/projectname?path=
```
# Authentication

Authentication is disabled when Django's debug setting is set to True. When Authentication is enabled
you will not be able to use any of the endpoints without rolling out and connecting your own Authentication/Authorization service. Authentication and permissions are handled by by the permissions app.

```
DEBUG = True
```

# Usage

First run Welder.

```
python manage.py runserver
```

You can then either interact programmatically using the examples in this document or use Postman
and Welder's Postman collection to send the API requests.

[![Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/085b6696a9590b87504b#?env%5BGroot%20Environment%5D=W3siZW5hYmxlZCI6dHJ1ZSwia2V5IjoidXNlciIsInZhbHVlIjoiYmVuIiwidHlwZSI6InRleHQifSx7ImVuYWJsZWQiOnRydWUsImtleSI6InByb2plY3QiLCJ2YWx1ZSI6Im15IHRlc3QiLCJ0eXBlIjoidGV4dCJ9LHsiZW5hYmxlZCI6dHJ1ZSwia2V5IjoiYXBpIiwidmFsdWUiOiJodHRwczovL3Rlc3Qud2V2b2x2ZXIuY29tIiwidHlwZSI6InRleHQifV0=)

```json
Environment = {
  "user": "wevovler (user)",
  "project": "mytest (project)",
  "api": "http://localhost:8000"
}
```

# Endpoints

## Create Project

Creates a bare repository with the provided username and project name.
The directory structure is generated uniquely using a hash of the username.
See: [here](https://github.com/blog/117-scaling-lesson-23742)

```python
python import requests
requests.post("/wevolver/mytest/create")
```

```shell
curl -X POST '/wevolver/mytest/create'
```

> The above command creates wevolver's project mytest and returns a message structured like this:

```json
{"message": "Created at ./repos/wevolver/mytest"}
```

### HTTP Request

 `POST /create/<username>/<projectname>`

## Delete Project

Deletes the specified project.

```python
python import requests
requests.post("/wevolver/mytest/delete")
```

```shell
curl -X POST '/wevolver/mytest/delete'
```

> The above command deletes wevolver's project mytest and returns a message structured like this:

```json
{"message": "Deleted at ./repos/wevolver/myest"}
```

### HTTP Request

`GET /<username>/<projectname>/delete`

## Read File

Gets the raw contents of the file at the specified path.

```python
python import requests
requests.get("/wevolver/mytest/readfile?path=readme.md")
```

```shell
curl -X GET '/wevolver/mytest/readfile?path=readme.md'
```

> The above command gets the file readme.md from wevolver's project my test and returns it structured like this:

```markdown
#mytest
This is where you should document your project  
### Getting Started
```

### HTTP Request
`GET /<username>/<projectname>/readfile?path=readme.md`

## Create Folder

Creates a new folder in the given directory. The folder path is specified by the
path parameter of the POST.

```python
python import requests
requests.post("/wevolver/wevolver/newfolder")
```

```shell
curl -X POST '/wevolver/mytest/newfolder'
```

> The above command creates the newfoler in wevolver's project mytest returns JSON structured like this:

```json
{
    "message": "Folder Created"
}
```

### HTTP Request

`POST /<username>/<projectname>/newfolder`

## Upload File

Uploads the attached files to the specified path.

```python
python import requests
with open('path') as fp:
    response = self.client.post('/{}/{}/upload?path={}'.format(user, app, "test.json"), {'file': fp})
```

> The above command returns JSON structured like this:

```json
{"message": "Files uploaded"}
```

### HTTP Request

`POST /<username>/<projectname>/upload?path=`

## List Full Bom

Looks through the entire project for any file named `bom.csv`. Each of these
files are concatenated and, once duplicates are removed, the master BOM is returned.

```python
python import requests
requests.get("/wevolver/mytest/listbom")
```

```shell
curl -X GET '/wevolver/mytest/listbom'
```

> The above command returns wevolver's project mytest's full bom as JSON structured like this:

```
name, url, quantity, price
test, google.com, 1, 1
```

### HTTP Request

`GET /<username>/<projectname>/listbom`

## Download Archive

Grabs and returns a user's repository as a tarball.

```python
python import requests
requests.get('/wevolver/mytest/download/archive')
```

```shell
curl -X GET '/wevolver/mytest/download/archive'
```

> The above command downloads the project directory zipped

### HTTP Request

`GET /<username>/<projectname>/archive/download`

## Read File Tree

Grabs and returns a single file or a tree from a user's repository.
The requested tree is first parsed into JSON.

```python
python import requests
requests.get("/wevolver/mytest?path=/")
```

```shell
curl -X GET '/wevolver/mytest?path=/'
```

> The above command reads wevolver's project mytest at path / and returns JSON structured like this:

```json
{
    "file": null,
    "tree": {
        "data": [
            {
                "name": "new",
                "type": "tree",
                "oid": "7b93b85c3a2aba0d510cc223ba15501b8c6283d5"
            },
            {
                "name": "readme.md",
                "type": "blob",
                "oid": "63c3ffbf73a78311a168a6949ec6bce690faa74e"
            }
        ]
    }
}
```

### HTTP Request

`GET /<username>/<projectname>?path=/`
