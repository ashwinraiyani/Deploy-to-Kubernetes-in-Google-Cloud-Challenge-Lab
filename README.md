# skillbadge6

# Task 1: Create a Docker image and store the Dockerfile
Run the following command in Cloud Shell.

gsutil cat gs://cloud-training/gsp318/marking/setup_marking.sh | bash

gcloud source repos clone valkyrie-app

cd valkyrie-app

cat > Dockerfile <<EOF

FROM golang:1.10

WORKDIR /go/src/app

COPY source .

RUN go install -v

ENTRYPOINT ["app","-single=true","-port=8080"]

EOF

docker build -t valkyrie-app:v0.0.1 .

cd ..

cd marking

./step1.sh

# Task 2: Test the created Docker image

cd ..

cd valkyrie-app

docker run -p 8080:8080 valkyrie-app:v0.0.1 &

cd ..

cd marking

./step2.sh  

# Task 3: Push the Docker image in the Container Repository
cd ..

cd valkyrie-app

docker tag valkyrie-app:v0.0.1 gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1

docker push gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1

sed -i s#IMAGE_HERE#gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1#g k8s/deployment.yaml

# Task 4: Create and expose a deployment in Kubernetes

gcloud container clusters get-credentials valkyrie-dev --zone us-east1-d

kubectl create -f k8s/deployment.yaml

kubectl create -f k8s/service.yaml

# Task 5: Update the deployment with a new version of valkyrie-app

git merge origin/kurt-dev

kubectl edit deployment valkyrie-dev

## In begining of file,  edit the field "replicas" from 1 to 3. And scroll down and bottom of file edit the field "replicas" from 1 to 3. Save Exit (Press Escape-> type :wq --> Enter)


docker build -t gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.2 .

docker push gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.2

kubectl edit deployment valkyrie-dev


## Change the image tag from v0.0.1 to v0.0.2. (two places need to changes) Save Exit (Press Escape-> type :wq --> Enter)

docker ps

**note down docker id**

docker kill <<put dockerID>>

# Task 6: Create a pipeline in Jenkins to deploy your app

export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")

kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &

printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

**Notedown Jenkins Password**

gcloud source repos list

**notedown URL**

Click on the Web Preview button in cloud shell, then click “Preview on port 8080” to connect to the Jenkins console.
Username: admin
password: Enter Jenkins Password
  

In the Jenkins user interface, click Credentials in the left navigation.

Click Manage Jenkins

Click Global credentials (unrestricted).

Click adding some credentials

Select Google Service Account from metadata from the Kind drop-down and click OK.

Click Jenkins > New Item in the left navigation:

Name the project "valkyrie-app", then choose the  Pipeline option and click OK.

On the next page, in the Branch Sources section, click Add Source and select git.

Paste the HTTPS clone URL of your sample-app repo in Cloud Source Repositories https://source.developers.google.com/p/YOUR_PROJECT/r/valkyrie-app into the Project Repository field. Remember to replace YOUR_PROJECT with your GCP Project ID.

From the Credentials drop-down, select the name of the credentials you created when adding your service account in the previous steps.

Click Save.


Now on cloud shell Run
  
  Open Jenkinsfile file in a text editor, and replace YOUR_PROJECT with your GCP project ID.
  
  

sed -i "s/green/orange/g" source/html.go

sed -i "s/YOUR_PROJECT/$GOOGLE_CLOUD_PROJECT/g" Jenkinsfile

git config --global user.email "**qwiklabs user email id**"

Click on Cloud Console --> Profile Icon >> Copy User Name (Something like *Student 34343*)
![screen](https://github.com/ashwinraiyani/skillbadge6/blob/main/62.png)
git config --global user.name "**Paste username here**" 
  
git add .

git commit -m 'green to orange'

git push origin master

Click on the Web Preview button in cloud shell, then click “Preview on port 8080” to connect to the Jenkins console.

Click On "Build Now"

It will take few minutes to complete the build

**Now Check the progress**
