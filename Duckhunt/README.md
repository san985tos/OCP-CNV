# Duckhunt

Start with creating a new project with following command:

```
oc new-project test
```

Now execute following command to deploy example application:

```
oc new-app nodejs~https://github.com/vrutkovs/DuckHunt-JS
```

Our application will now build from source, you can watch it happen by tailing the build log file. When it's finished it will push the image into the OpenShift image registry:

```
oc logs duckhunt-js-1-build -f
```

Once finished, check the status of the pods by executing the command below:

```
oc get pods
```

Now expose the application (via the service) so we can route to it from the outside...

```
oc expose svc/duckhunt-js
```

To check the route execute following command:

```
oc get route duckhunt-js
```

Open the route and enjoy
