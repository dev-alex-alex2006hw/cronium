# Dockerized Cronium Google Cloud

Supports:

* Google Cloud (GCE)

# Make It Short!

In short, you can define periodic tasks your cloud environments.

Example:

~~~~
$ docker run -it --rm \
    -v $(pwd)/auth.json:/auth.json \
    -e "GCLOUD_ACCOUNT_FILE=/auth.json" \
    -e "GCLOUD_ACCOUNT_EMAIL=useraccount@developer.gserviceaccount.com" \
    -e "JOB_NAME1=ListingInstances" \
    -e "JOB_COMMAND1=gcloud compute instances list" \
    -e "JOB_TIME1=* * * * *" \
    -e "JOB_ON_ERROR1=continue" \
    blacklabelops/cronium:gce \
$
~~~~

> The periodic command is executed inside the authenticated container. This works both with json and P12 key files.


# How It Works

This container is using blacklabelops/cronium for defining jobs. See this link for a comprehensive documentation: (blacklabelops/cronium)[https://github.com/blacklabelops/cronium]

# Google Cloud API

You can run commands against existing cloud projects!

Documentation can be found here: [Creating & Deleting Projects](https://developers.google.com/console/help/new/#creatingdeletingprojects)

Also you will have to activate APIs manually before you can use them!

Documentation can be found here: [Activating & Deactivating APIs](https://developers.google.com/console/help/new/#activating-and-deactivating-apis)

## Google Cloud Authentication

There are two ways to authenticate the gcloud tools and execute gcloud commands. Both ways need
a Google Cloud OAuth Service Account file. This is documented here: [Service Account Authentication](https://cloud.google.com/storage/docs/authentication?hl=en#service_accounts).

You can now mount the file into your container and execute commands like this:

~~~~
$ docker run -it --rm \
    -v $(pwd)/auth.json:/auth.json \
    -e "GCLOUD_ACCOUNT_FILE=/auth.json" \
    -e "GCLOUD_ACCOUNT_EMAIL=useraccount@developer.gserviceaccount.com" \
    blacklabelops/cronium:gce \
    bash
$ gcloud compute instances list
~~~~

> Opens the bash console inside the container, the second command is executed inside the authenticated container. This works both with json and P12 key files.

You can also Base64 encode the authentication file and stuff it inside an environment variable. This works perfect for long-running stand-alone containers.

~~~~
$ docker run -it --rm \
    -e "GCLOUD_ACCOUNT=$(base64 auth.json)" \
    -e "GCLOUD_ACCOUNT_EMAIL=useraccount@developer.gserviceaccount.com" \
    blacklabelops/cronium:gce \
    bash
$ gcloud compute instances list
~~~~

> Opens the bash console inside the container, the second command is executed inside the authenticated container. This works both with json and P12 key files.

## Setting the Google Cloud Project

Set your default Google Project by defining the CLOUDSDK_CORE_PROJECT environment variable.

~~~~
$ docker run -it --rm \
    -e "GCLOUD_ACCOUNT=$(base64 auth.json)" \
    -e "GCLOUD_ACCOUNT_EMAIL=useraccount@developer.gserviceaccount.com" \
    -e "CLOUDSDK_CORE_PROJECT=example-project" \
    blacklabelops/cronium:gce \
    bash
$ gcloud compute instances list
~~~~

> Runs all commands against the project `example-project`.

## Setting the Google Cloud Zone and Region

Set your default Google Project Zone and Region with the environment variables CLOUDSDK_COMPUTE_ZONE and
CLOUDSDK_COMPUTE_REGION.

The documentation can be found here : [Regions & Zones](https://cloud.google.com/compute/docs/zones?hl=en)

Example:

~~~~
$ docker run -it --rm \
    -e "GCLOUD_ACCOUNT=$(base64 auth.json)" \
    -e "GCLOUD_ACCOUNT_EMAIL=useraccount@developer.gserviceaccount.com" \
    -e "CLOUDSDK_CORE_PROJECT=example-project" \
    -e "CLOUDSDK_COMPUTE_ZONE=europe-west1-b" \
    -e "CLOUDSDK_COMPUTE_REGION=europe-west1" \
    blacklabelops/cronium:gce \
    bash
$ gcloud compute zones list
$ gcloud compute regions describe ${CLOUDSDK_COMPUTE_REGION}
~~~~

> Set your region and zone to belgium. More details appear with the `describe` command.

# References

* [Cronium](https://github.com/blacklabelops/cronium)
* [Docker Homepage](https://www.docker.com/)
* [Docker Userguide](https://docs.docker.com/userguide/)
