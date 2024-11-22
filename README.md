 This journey began with understanding container images and containers, essential for isolating and running applications independently in diverse environments. You then embarked on the practical task of creating a container image, involving the assembly of key files such as demo.py, requirements.txt, and Dockerfile.

Testing these components locally ensured your application’s smooth functionality before deploying it through Docker. Subsequently, you delved into the realms of IBM Code Engine, a robust platform that not only hosts your containerized workloads but also simplifies the deployment process by handling the underlying infrastructure complexities.

The final steps of your journey involved leveraging IBM Cloud’s resources to build and push your container image to a registry, followed by deploying your application and making it accessible through a public URL. This comprehensive process, from local testing to cloud deployment, highlights the seamless integration of Gradio, Docker, and IBM Code Engine, culminating in a successful application deployment on IBM Cloud.# deploymentIBMCloud
involved leveraging IBM Cloud’s resources to build and push your container image to a registry, followed by deploying your application and making it accessible through a public URL. 

Steps involved are:
1. Creating directory myapp
2. Creating virtual env
3. Running requirements.txt file
4. Running python file will run the application.
5. creating build configuration

To create a build configuration that pulls code from a local directory, use the build create command and specify the build-type as local.

Since you also need Code Engine to store the image in the IBM Cloud Container Registry, you need to provide your registry access secret so that Code Engine can access and push the build result to the registry in your namespace.

Run the following command in your Code Engine CLI to create a build configuration:

   ibmcloud ce build create --name build-local-dockerfile1 \
                        --build-type local --size large \
                        --image us.icr.io/${SN_ICR_NAMESPACE}/myapp1 \
                        --registry-secret icr-secret
                        /

With the build create command, you create a build configuration called build-local-dockerfile1 and you specify local as the value for --build-type. The size of the build defines how many resources such as CPU cores, memory, and disk space are assigned to the build. You specify size as large if your model pipeline that will be downloaded into a container requires lots of resources to run.

You also provide the location of the image registry, which is the namespace us.icr.io/${SN_ICR_NAMESPACE} in the IBM Cloud Container Registry. You can also replace ${SN_ICR_NAMESPACE} with your ICR namespace provided by Code Engine. Your ICR namespace can be seen in Code Engine page -> project information.

The container image will be tagged (named) as myapp1. You specify the --registry-secret option to access the registry with the icr-secret. 

6. To submit a build run from a build configuration with the CLI that pulls the source from a local directory, use the buildrun submit command. This command requires the name of the build configuration and the path to your local source. Other optional arguments can be specified.

When you submit a build that pulls code from a local directory, your source code is packed into an archive file and uploaded to your IBM Cloud Container Registry instance. Note that you can only target the IBM Cloud Container Registry for your local builds. The source image is created in the same namespace as your build image.

Run the following command in your Code Engine CLI to submit and run the build configuration:

ibmcloud ce buildrun submit --name buildrun-local-dockerfile1 \
                            --build build-local-dockerfile1 \
                            --source .
                            /

7. To monitor the progress of the buildrun, use the following command: ibmcloud ce buildrun get -n buildrun-local-dockerfile1

   Once you see the status showing Succeeded (same as the following screenshot), that means your container image has been created successfully and pushed to the registry under your namespace.

8.Code Engine lets you deploy an application that uses an image stored in IBM Cloud Container Registry with the ibmcloud app create command.

Run the following command in your Code Engine CLI to deploy the app:

ibmcloud ce application create --name demo1 \
                            --image us.icr.io/${SN_ICR_NAMESPACE}/myapp1  \
                            --registry-secret icr-secret --es 2G \
                            --port 7860 --minscale 1


There are some important arguments specified in the command:
1. The deployed app will be called demo1.
2. It references (uses) the image us.icr.io/sn-labs-xintongli/myapp1.
3. It uses 2G of ephemeral storage in the container. For smaller applications, a default value for the ephemeral storage, i.e. --es 400MB, might be enough. You need 2G because of the size of the GPT2 model.
4. You need to specify the port number 7860 for the application so that the public network connections and requests can be directed to the application when they arrive at the server.
5. You set --minscale 1 to make sure that your app will keep running even though there are no continuous requests. This is important because otherwise, you would need to wait for the app to start running every time you access its URL.

This means your app has been deployed and you can access it now! To obtain the URL of your app, run ibmcloud ce app get --name demo1 --output url. Click on the URL returned, and you should be able to see your app running in your browser!


Testing these components locally ensured your application’s smooth functionality before deploying it through Docker. Subsequently, you delved into the realms of IBM Code Engine, a robust platform that not only hosts your containerized workloads but also simplifies the deployment process by handling the underlying infrastructure complexities.

The final steps of your journey involved leveraging IBM Cloud’s resources to build and push your container image to a registry, followed by deploying your application and making it accessible through a public URL. This comprehensive process, from local testing to cloud deployment, highlights the seamless integration of Gradio, Docker, and IBM Code Engine, culminating in a successful application deployment on IBM Cloud.

