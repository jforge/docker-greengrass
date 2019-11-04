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

Use the workflow to create a Group ```cybus_universe```. This finally produces:
- A new Greengrass group
- Provisioning of a new core for this group in the IoT registry
- Create of a public-private key pair for the core.
- Generation of a new security certificate for the core using the key
- Adding a default security policy to the certificate.




## Some device sample (deferred)

Define a device and some pre-processing on it's data for publishing to an external MQTT Broker.

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

