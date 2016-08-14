# Monitoring Containers
  In this chapter we are going to monitor logs from container. This can be achieved by using the various monitoring tools, we use

    * Papertrail - cloud-hosted log management
    * Datadog - Cloud Monitoring as a Service

## Papertrail

* Go to https://papertrailapp.com/signup?plan=free

* Sign up for the service

  ![signup](images/monitoring/1.png)

* Once sign up choose `Add a system`

  ![system](images/monitoring/2.png)

* This is the URL to which we will ship the logs. Copy the URL for using in rsyslog configuration

  ![url](images/monitoring/3.png)

  _Note: Url will be different for you_

### Setting up Logspout Container

To send logs from applications running in a Docker container, we use a small container developed by [gliderlabs](https://hub.docker.com/r/gliderlabs/logspout/)

To start a logspout container, run: [Reference](http://help.papertrailapp.com/kb/configuration/configuring-centralized-logging-from-docker/)

    docker run --restart=always -d -p 80:80\
      -v=/var/run/docker.sock:/var/run/docker.sock gliderlabs/logspout  \
      syslog://logs4.papertrailapp.com:10656

Replace `logs4.papertrailapp.com:10656` with one of your Papertrail log destinations.

Now this contaner run in a background with port mapping 80:80.

### Monitoring logs

* Once there is an entry for log in other container starts, Logspout Container helps to collect the logs from various Containers to papertrail automatically.

* Visit `Dashboard` in [papertrail](https://papertrailapp.com/dashboard).

  ![Dashboard](images/monitoring/4.png)

* Click `All Systems` from Dashboard to view and edit the setings of various systems _(in our case containers)_.

  ![All Systems](images/monitoring/5.png)

  Change the name of each system with respect to container for easy understanding.

* Click `Events` to monitor logs.

  ![Events](images/monitoring/6.png)

### Creating an Alert

* To create an alert based on log, `search` for the entry and `Save Search`.

  ![Alert](images/monitoring/7.png)

* `Save Search` will open a dialog in which you can choose a name and click `Save and Setup an Alert`

  ![Save Search](images/monitoring/8.png)

* It will redirect to page in which you can choose various forms of alert like mail, hipchat, slack, campfire, etc...

  ![Mail](images/monitoring/9.png)

* Once the settings are provoided and created an alert, settings are saved and we can Update, delete and modify the search.

  ![Mail](images/monitoring/10.png)

## Verifying an alert

* We get an alert in mail once the query based on our search is occured.

  ![Mail alert](images/monitoring/11.png)

  ![Mail alert](images/monitoring/12.png)

## Datadog

* Signup for a 14 day trial account in [datadog](https://app.datadoghq.com/signup)

  ![Signup](images/monitoring/13.png)

#### Installation

  * Once signup login to the console choose `Integrations` --> `Agent` in it then proceed with installing for Ubuntu to monitor host.

    ![Integrations container](images/monitoring/15.png)

    Use this command to install a `datadog agent` in host using a script.

    ```
    DD_API_KEY=1ba0c0a88c3dcf52b076886045f47a8c bash -c "$(curl -L https://raw.githubusercontent.com/DataDog/dd-agent/master/packaging/datadog-agent/source/install_agent.sh)"
    ```

    Once it is done after few minutes the datadog agent will start monitoring host.

    You can stop monitoring by stopping the agent by running the following command.

        sudo /etc/init.d/datadog-agent stop

  * To monitor containers again from console choose `Integrations` --> `Agent` in it then proceed with installing docker container to monitor other containers.

    ![Integrations container](images/monitoring/14.png)

    Use this command to install a `datadog docker-dd-agent` in host.

    ```
    docker run -d --name dd-agent -h `hostname` -v /var/run/docker.sock:/var/run/docker.sock -v /proc/:/host/proc/:ro -v /sys/fs/cgroup/:/host/sys/fs/cgroup:ro -e API_KEY=1ba0c0a88c3dcf52b076886045f47a8c datadog/docker-dd-agent:latest
    ```

#### Monitoring

  * Once it is done after few minutes the datadog agent will start monitoring host and other containers.

  * We can monitor this from console by visiting `Infrastructure` and choosing the `Hostname`.

    ![Monitoring](images/monitoring/16.png)

  * Datadog monitoring other containers can be viewed by clicking the `inspect` from hostname.

    ![Monitoring_container](images/monitoring/17.png)

  * It will open a dialog in which you can choose docker dashboard to view the complete monitoring report of docker.

    ![Container_Apps](images/monitoring/18.png)

  * By choosing docker dashboard, will open up new page displaying the complete information about other containers.

    ![Dashboard](images/monitoring/19.png)