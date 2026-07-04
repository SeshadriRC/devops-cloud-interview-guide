1. Write a shell scripting where you need to print Today's date with your name , it should run interval of 5 seconds, name need to pass as an env variable.

export name="Welcome"

```bash
#!/bin/bash

while true
do
    echo "$(date) - $name"
    sleep 5
done
```


dockerize it
-----------

```bash
FROM ubuntu

WORKDIR /tmp

COPY . .

ENV name=Welcome

CMD ["/tmp/script.sh"]
```

---

**Convert to kubernetes pod yaml**

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-script-app
spec:
  containers:
  - name: my-script-app
    image: myscript:v1
```

**Now override the ENV in pod yaml**

```yml 
apiVersion: v1
kind: Pod
metadata:
  name: my-script-app-2
spec:
  containers:
  - name: my-script-app
    image: myscript:v1
    env:
	- name: name
	  value: Coffee
```

**Now using helm override the value**


`helm create my-script`

- go inside templates folder and delete all yaml files, test directory , then nullify notes.txt and values.yaml
- copy the pod.yaml to templates folder

- helm install release-name my-script  -> now pod will be running

- then parameterize only the env value

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-script-app-2
spec:
  containers:
  - name: my-script-app
    image: myscript:v1
    env:
    - name: name
      value: {{ .Values.scriptname }}
```


values.yml file

`scriptname: sesha-script`

- Then run and see helm template command

`helm template my-script`

```yml
# Source: my-script/templates/pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: my-script-app-2
spec:
  containers:
  - name: my-script-app
    image: myscript:v1
    env:
    - name: name
      value: sesha-script
```


`helm install release-1 my-script`

$ kubectl logs my-script-app-2
Tue Jun 30 11:14:47 UTC 2026 - sesha-script
Tue Jun 30 11:14:53 UTC 2026 - sesha-script
Tue Jun 30 11:14:58 UTC 2026 - sesha-script
Tue Jun 30 11:15:03 UTC 2026 - sesha-script
