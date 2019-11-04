# 1h-POC - Getting familiar with AWS IoT Greengrass using Docker.

## AWS IoT Greengrass with Docker

AWS IoT Greengrass provides cloud capabilities to a local environment. It is qualified as a containerized AWS Lambda runtime environment with prebuilt connectos for serverless apllications. Edge computing requirements can be supported as far as local resources can be made accessible for these local devices. 

## Objective

Run IoT Greengrass in a Docker Container, evaluate possible interfaces to/from external resources.

## Go get Greengrass running

### Preparation
First of all install the AWS CLI onto your docker-running host.

Ensure you IAM user to have the permission to pull and list images from ECR.

### Get the Docker Image

Get a pre-defined login with token for the specific AWS Docker registry:
```
aws ecr get-login --registry-ids 216483018798 --no-include-email --region us-west-2
```

Execute the output of the call, which is a docker login:
```
 docker login -u AWS -p eyJwYXlsW5ZR...aW9uIjoxNUyfQ== https://216483018798.dkr.ecr.us-west-2.amazonaws.com
```

Get a list of available images:
```
aws ecr list-images --region us-west-2 --registry-id 216483018798 --repository-name aws-iot-greengrass
```

Pull the latest image:
```
docker pull 216483018798.dkr.ecr.us-west-2.amazonaws.com/aws-iot-greengrass:latest
```


 
### Create and configure Greengrass Group and Core

Greengrass doesn't work without a greengrass group and core. For configuration of the pulled image for a properly set up container certificates and the core configuration file is required (can be ommitted when configuring and download IoT core via amazon console).

Login to AWS console and switch to [IoT Greengrass](https://eu-central-1.console.aws.amazon.com/iot/home?region=eu-central-1#/greengrassIntro).

Use the workflow to create a Group ```cybus_universe```. 

This finally produces:
- A new Greengrass group
- A new core for this group in the IoT registry
- A public-private key pair for the core.
- A new security certificate for the core using the key
- A default security policy added to the certificate.

The final artifact to be downloaded is a gzipped tar containing the configuration file and certificates.


### Configure and startup a Greengrass docker container

Extract the archive downloaded above to a known location
```
export gg_path=~/opt/greengrass/
mkdir $gg_path
tar xvzf <hashcode>-setup.tar.gz -C $gg_path
```

Root CA certificates are required to properly enable devices to connect to AWS IoT over TLS.
```
cd $gg_path
wget -O root.ca.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
```

Start the container using the certificates and configuration file:
```
docker run --rm --init -it --name aws-iot-greengrass \
--entrypoint /greengrass-entrypoint.sh \
-v $gg_path/certs:/greengrass/certs \
-v $gg_path/config:/greengrass/config \
-p 8883:8883 \
216483018798.dkr.ecr.us-west-2.amazonaws.com/aws-iot-greengrass:latest
```


## Some functional sample

Define things and lambdas for publishing pre-processed data to an external system (e.g. MQTT Broker).

### Prepare the Greengrass group

When running Greengrass with docker, the lambda execution environment must be set to "No container" as the containerization option. Change this in the created IoT Greengrass group settings.

![Greengrass Lambda containerization option](images/greengrass-lambda-containerization-option.jpg)



### Make a thing and provide a function


### Publish to an external resource


## Result

Seems to be possible in a non-experimental resp. acceptable manner.

## Resources

- https://docs.aws.amazon.com/en_en/greengrass/latest/developerguide/what-is-gg.html
- https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-core.html
- https://docs.aws.amazon.com/en_en/greengrass/latest/developerguide/gg-config.html
- https://github.com/awslabs/aws-greengrass-group-setup
- https://docs.aws.amazon.com/greengrass/latest/developerguide/lambda-functions.html
- https://docs.aws.amazon.com/en_en/greengrass/latest/developerguide/run-gg-in-docker-container.html
- https://github.com/alpine-docker/greengrass

