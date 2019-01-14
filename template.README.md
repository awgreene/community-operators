# Steps to create the community operators resources

1. Create a directory named after your operator in the `community-operators` directory.
2. Create the required operator resources, templates for the required resources can be found in the `templates` directory:
    ```bash
    $ ls rh-operators/my-operator
    my-operator.v1.0.0.clusterserviceversion.yaml
    my-operator-crd1.crd.yaml
    my-operator-crd2.crd.yaml
    my-operator.package.yaml
    ```
3. For detailed steps on how to create the CSV [follow this guide to create an OLM-compatible CSV for your operator](https://github.com/operator-framework/operator-lifecycle-manager/blob/master/Documentation/design/building-your-csv.md)

An operator's CSV must contain the annotations mentioned [here](https://github.com/operator-framework/operator-marketplace/blob/master/docs/marketplace-required-csv-annotations.md) for it to be displayed properly within the Marketplace UI.

## Testing your csv

We will walk through the exercise of testing the [Jager operator](https://github.com/jaegertracing/jaeger-operator)
1. Create a new project
```bash
$ oc new-project test
```
1. Make sure you can deploy the Jaeger Operator by following [these steps](https://github.com/jaegertracing/jaeger-operator)
```bash
oc create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/master/deploy/crds/io_v1alpha1_jaeger_crd.yaml
oc create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/master/deploy/service_account.yaml
oc create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/master/deploy/role.yaml
oc create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/master/deploy/role_binding.yaml
oc create -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/master/deploy/operator-openshift.yaml
```
2. Confirm that the operator is running and make sure that the logs seem reasonable:
    ```bash
    $ oc get pods
    NAME                               READY     STATUS    RESTARTS   AGE
    jaeger-operator-59d4dd46df-dtgr6   1/1       Running   0          7s

    $ oc logs jaeger-operator-59d4dd46df-dtgr6
    time="2019-01-10T02:40:16Z" level=info msg=Go arch=amd64 os=linux version=go1.11.1
    time="2019-01-10T02:40:16Z" level=info msg=operator-sdk version=v0.1.1
    time="2019-01-10T02:40:16Z" level=info msg="Registering Components."
    time="2019-01-10T02:40:16Z" level=info msg="Starting the Cmd."
    time="2019-01-10T02:40:16Z" level=info msg="Reconciling Deployment" name=jaeger-operator namespace=test
    time="2019-01-10T02:40:16Z" level=info msg="Reconciling Deployment" name=jaeger-operator namespace=test
    ```

3. If the operator looks like it's working, create and apply a cr that the operator watches:
    ```bash
    $ cat jaeger.cr.yaml 
    apiVersion: io.jaegertracing/v1alpha1
    kind: Jaeger
    metadata:
    name: simplest

    $ oc apply -f jaeger.cr.yaml
    ```

4. Confirm that the expected behavior is observed, in this case a `simplest` pod should have been created
    ```
    $ oc get pods
    NAME                               READY     STATUS    RESTARTS   AGE
    jaeger-operator-59d4dd46df-dtgr6   1/1       Running   0          5m
    simplest-588b898dd7-blr9n          1/2       Running   0          14s
    ```

5. Check the logs of the pods to make sure things are working as intended
    ```bash
    $ oc logs jaeger-operator-59d4dd46df-dtgr6

    $ oc logs simplest-588b898dd7-blr9n -c jaeger
    ```

6. At this point, the operator seems to be working, so delete the operator and the cr.
    ```bash
    $ oc delete -f jaeger.cr.yaml

    $ oc delete -f https://raw.githubusercontent.com/jaegertracing/jaeger-operator/master/deploy/operator-openshift.yaml
    ```

7. Open up another terminal, and print the logs from the OLM:
    ```
    $ oc get pods -n openshift-operator-lifecycle-manager
    NAME                               READY     STATUS    RESTARTS   AGE
    catalog-operator-b47bcc48b-8mn9d   1/1       Running   0          6h
    certified-operators-njzjs          1/1       Running   0          6h
    olm-operator-75b75f6657-9zlsq      1/1       Running   0          6h
    olm-operators-7jjzh                1/1       Running   0          6h
    packageserver-856c4d8bc9-5qsq5     1/1       Running   0          6h
    rh-operators-kc749                 1/1       Running   0          6h

    $ oc -n openshift-operator-lifecycle-manager logs olm-operator-75b75f6657-9zlsq -f
    ```
8. If you find a `Requirements not met` message, make sure to check the csv
```
$ oc get csvs
$ oc describe csv <CSV NAME>
```

8. Apply the csv you created earlier and repeat steps 2 through 5.