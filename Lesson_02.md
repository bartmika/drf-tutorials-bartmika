# Lesson 2: Fullstack Development
## Step 1:
Please install the Google Chrome extension via [Allow CORS: Access-Control-Allow-Origin](https://chrome.google.com/webstore/detail/allow-cors-access-control/lhobafahddgcelffkeicbaginigeejlf?hl=en)

## django-cors-headers
While you are inside the **backend** project, and your ``virtualenv`` is activated, please install this library.

Notes:
* When you run the frontend please activate this plugin and when you finish please deactivate it.
* Would you like to know more? Please read the [developer documentation](https://github.com/adamchainz/django-cors-headers).

```bash
pip install django-cors-headers
```

And then add it to your installed apps:

```python
# ...

INSTALLED_APPS = [
    # ...
    'corsheaders',
    # ...
]

# ...
```

You will also need to add a middleware class to listen in on responses:

```python
MIDDLEWARE = [
    # ...
    'corsheaders.middleware.CorsMiddleware', # Note: Place this middleware above the 'CommonMiddleware'.
    'django.middleware.common.CommonMiddleware',
    # ...
]
```

Finally add the following to the bottom of the ``settings.py`` file

```python
# ...

# django-cors-headers
# https://github.com/adamchainz/django-cors-headers
CORS_ORIGIN_ALLOW_ALL = True
```

## Setup frontend

Create our project repository

```bash
mkdir burrow-front
cd burrow-front
```

Create a ``hello.html`` file with the following content:

```html
<!DOCTYPE html>
<html>
<body>

<h1>Hello API</h1>
<h2>GET</h2>
<p id="getDemo">Text</p>
<button type="button" onclick="submitGetRequest()">GET</button>

<script>
function submitGetRequest() {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
        const stringData =  this.responseText;
        const dictData = JSON.parse(stringData);
        document.getElementById("getDemo").innerHTML = dictData.detail;
    }
  };
  xhttp.open("GET", "http://127.0.0.1:8000/hello", true);
  xhttp.setRequestHeader("Content-type", "application/json");
  xhttp.setRequestHeader("Accept", 'application/json');
  xhttp.send();
}
</script>

<h2>POST</h2>
<p id="postDemo">Text</p>
<button type="button" onclick="submitPostRequest()">POST</button>

<script>
function submitPostRequest() {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
        const stringData =  this.responseText;
        const dictData = JSON.parse(stringData);
        document.getElementById("postDemo").innerHTML = dictData.detail;
    }
  };
  xhttp.open("POST", "http://127.0.0.1:8000/hello", true);
  xhttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
  xhttp.setRequestHeader("Accept", 'application/json');
  xhttp.send(JSON.stringify({
      "say": "This is my submission text!"
  }));
}
</script>

</body>
</html>
```

Go ahead and try clicking the buttons. You will see the code working.
