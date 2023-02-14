```shell
oc new-app --name http-s2i httpd:2.4~https://github.com/adrien-legros/openshift-s2i-demo --context-dir html
oc expose svc http-s2i
```

```shell
oc new-app python-s2i python:3.8-ubi8~https://github.com/adrien-legros/openshift-s2i-demo --context-dir python-dependencies
oc expose svc python-s2i
```