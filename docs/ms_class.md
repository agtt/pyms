# Microservices class

The class Microservice is the core of all microservices built with PyMS. 


You can create a simple microservice such as:

```python
from flask import jsonify

from pyms.flask.app import Microservice

ms = Microservice(service="my-minimal-microservice", path=__file__)
app = ms.create_app()


@app.route("/")
def example():
    return jsonify({"main": "hello world"})


if __name__ == '__main__':
    app.run()
```

And a config file like this config.yml

```yaml
my-minimal-microservice:
  APP_NAME: "Python Microservice"
```
Check [Configuration](configuration.md) section to know how to create a configuration file.

Each keyword in our configuration block, can be accessed in our Microservice object through the attribute `config`.

```yaml
# Config.yml
example-config:
  APP_NAME: "Python Microservice"
  foo: "var"
  multiplevars:
    config1: "test1"
    config2: "test2"
  
```
```python
#app.py
from pyms.flask.app import Microservice

ms = Microservice(service="example-config", path=__file__)
print(ms.config.APP_NAME) 
# >> "Python Microservice"
print(ms.config.foo) 
# >> "bar"
print(ms.config.multiplevars.config1) 
# >> "test1"
print(ms.config.multiplevars.config2) 
# >> "test2"
```



# Looking for Configuration file
By default, Microservice class search a config.yml in the same path. You can set a different route or set a json file.
To change this path, define a environment variable `CONFIGMAP_FILE`.

This way of looking for the configuration is useful when you work with Docker and Kubernetes. For example, you can integrate
a configmap of Kubernetes, with this microservice and a deployment with:

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: my-microservice
spec:
  replicas: 1
  template:
    spec:
      containers:
      - name: my-microservice
        image: ...
        env:
          - name: CONFIGMAP_FILE
            value: "/usr/share/microservice/config.yaml"

        volumeMounts:
          - mountPath: /usr/share/microservice
            name: ms-config-volume
      volumes:
        - name: ms-config-volume
          configMap:
            name: my-microservice-configmap
```

Check more examples in [this Github page](https://github.com/python-microservices/pyms/tree/master/examples)