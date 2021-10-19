Helm
^^^^^^

1. Install Help
    - choco install kubernetes-helm (For windows 10)

2. Initialize a Helm Chart Repository
    - $ helm repo add bitnami https://charts.bitnami.com/bitnami
2.1. Once this is installed, you will be able to list the charts you can install:
    - $ helm search repo bitnami

3. Install an Example Chart
    To install a chart, you can run the helm install command. Helm has several ways to find and install a chart, but the easiest is to use the bitnami charts.
3.1 Make sure we get the latest list of charts
    - $ helm repo update
    - $ helm install bitnami/mysql --generate-name

4. You get a simple idea of the features of this MySQL chart by running:
    - $ helm show chart bitnami/mysql.
    Or you could run:
    - $ helm show all bitnami/mysql 
    to get all information about the chart.

So one chart can be installed multiple times into the same cluster. And each can be independently managed and upgraded.

5. Learn About Releases
    - $ helm list or help ls

6. Uninstall a Release
    - $ helm uninstall mysql-1612624192
    This will uninstall mysql-1612624192 from Kubernetes, which will remove all resources associated with the release as well as the release history.

    If the flag --keep-history is provided, release history will be kept. You will be able to request information about that release:
    - $ helm status mysql-1612624192
    Because Helm tracks your releases even after you've uninstalled them, you can audit a cluster's history, and even undelete a release (with helm rollback).
===================================================================================================================================================================

Getting Started
^^^^^^^^^^^^^^^^^^

1. A Starter Chart - we'll create a simple chart
    - $ helm create mychart
    Expected to looks like this:
        mychart/
            Chart.yaml
            values.yaml
            charts/
            templates/
            ...

2. A First Template - The first template we are going to create will be a ConfigMap.
    In Kubernetes, a ConfigMap is simply an object for storing configuration data. Other things, like pods, can access the data in a ConfigMap.

    With this simple template, we now have an installable chart. And we can install it like this:
        - $ helm install full-coral ./mychart
    
    Using Helm, we can retrieve the release and see the actual template that was loaded.
        - $ helm get manifest full-coral
    
    Now we can uninstall our release: 
        - $ helm uninstall full-coral.