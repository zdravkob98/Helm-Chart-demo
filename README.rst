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
        - $ helm uninstall full-coral

3. Adding a Simple Template Call - Hard-coding the name: into a resource is usually considered to be bad practice. 
    Names should be unique to a release. So we might want to generate a name field by inserting the release name.

    Let's alter configmap.yaml accordingly.
        name field now is: {{ .Release.Name }}-configmap.

    Now when we install our resource, we'll immediately see the result of using this template directive:
        - $ helm install clunky-serval ./mychart
    
    You can run this command to see the entire generated YAML.
        - $ helm get manifest clunky-serval 

    When you want to test the template rendering, but not actually install anything, you can use:
        - $ helm install --debug --dry-run goodly-guppy ./mychart
        This will render the templates. But instead of installing the chart, it will return the rendered template to you so you can see the output


Built-in Objects - Objects are passed into a template from the template engine.
^^^^^^^^^^^^^^^^^

Values Files
^^^^^^^^^^^^^^^

One of the built-in objects is Values. This object provides access to values passed into the chart. Its contents come from multiple sources:

    - The values.yaml file in the chart
    - If this is a subchart, the values.yaml file of a parent chart
    - A values file if passed into helm install or helm upgrade with the -f flag (helm install -f myvals.yaml ./mychart)
    - Individual parameters passed with --set (such as helm install --set foo=bar ./mychart)

1. Let's edit mychart/values.yaml and then edit our ConfigMap template.
    removeing the defaults in values.yaml and send just one parameters
        - favoriteDrink: coffee
    Now we can use this inside of a template:

        apiVersion: v1
        kind: ConfigMap
        metadata:
            name: {{ .Release.Name }}-configmap
        data:
            myvalue: "Hello World"
            drink: {{ .Values.favoriteDrink }}

    We expected to render it:
        - $ helm install geared-marsupi ./mychart --dry-run --debug
    
    Because favoriteDrink is set in the default values.yaml file to coffee, that's the value displayed in the template.
    We can easily override that by adding a --set flag in our call to helm install:
        - $ helm install solid-vulture ./mychart --dry-run --debug --set favoriteDrink=slurm
        We expected to see in our data - slurm. Since --set has a higher precedence than the default values.yaml file, our template generates drink: slurm.
    
    Values files can contain more structured content, too. For example, we could create a favorite section in our values.yaml file, and then add several keys there:
        favorite:
            drink: coffee
            food: pizza
    And now we would have to modify the template slightly:
        apiVersion: v1
            kind: ConfigMap
            metadata:
                name: {{ .Release.Name }}-configmap
            data:
            myvalue: "Hello World"
                drink: {{ .Values.favorite.drink }}
                food: {{ .Values.favorite.food }}

2. Deleting a default key
    - If you need to delete a key from the default values, you may override the value of the key to be null,
    in which case Helm will remove the key from the overridden values merge.