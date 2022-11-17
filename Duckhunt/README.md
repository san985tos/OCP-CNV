# Duckhunt

Start with creating a new project with following command:

```
oc new-project test
```

Now execute following command to deploy example application:

```
oc new-app nodejs~https://github.com/vrutkovs/DuckHunt-JS
```

This will create all Kubernetes resources to deploy and run the example application as below:

```
--> Creating resources ...
    imagestream.image.openshift.io "duckhunt-js" created
    buildconfig.build.openshift.io "duckhunt-js" created
    deployment.apps "duckhunt-js" created
    service "duckhunt-js" created
--> Success
    Build scheduled, use 'oc logs -f buildconfig/duckhunt-js' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose service/duckhunt-js'
    Run 'oc status' to view your app.
```

Our application will now build from source, you can watch it happen by tailing the build log file. When it's finished it will push the image into the OpenShift image registry:

```
oc logs duckhunt-js-1-build -f
```

```
This process will likely take approximately 3-4 minutes for the following output to confirm the new image is pushed to registry:

Successfully pushed image-registry.openshift-image-registry.svc:5000/test/duckhunt-js@sha256:c4e64bc633ae09ce0f2f2f6de2ca9eaca8e11dc5b335301a2be78216df4b6929
Push successful
NOTE: You may get an error saying "Error from server (BadRequest): container "sti-build" in pod "duckhunt-js-1-build" is waiting to start: PodInitializing"; you were just too quick to ask for the log output of the pods, simply re-run the command.
```

Once finished, check the status of the pods by executing the command below:

```
oc get pods
```

You'll see that a couple of pods have been created, one that just completed our build, and then the application itself, which should be in a Running state, if it's still showing as ContainerCreating just give it a few more seconds:

```
NAME                           READY   STATUS      RESTARTS   AGE
duckhunt-js-1-build            0/1     Completed   0          4m7s
duckhunt-js-5b75fd5ccf-j7lqj   1/1     Running     0          105s   <-- this is our app!
```

Now expose the application (via the service) so we can route to it from the outside...

```
oc expose svc/duckhunt-js
```

As a result, a route is created:

```
route.route.openshift.io/duckhunt-js exposed

To check the route execute following command:
```

oc get route duckhunt-js

```

Now you should be able to see the route endpoint as below:
```

NAME HOST/PORT PATH SERVICES PORT TERMINATION WILDCARD
duckhunt-js duckhunt-js-test.apps.bvqlc.dynamic.opentlc.com duckhunt-js 8080-tcp None

```

```
