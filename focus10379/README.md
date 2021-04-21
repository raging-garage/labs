See https://google.qwiklabs.com/focuses/10379

Setup
-----

> Create all resources in the us-east1 region and us-east1-b zone, unless otherwise directed.

    gcloud config set compute/region us-east1
    gcloud config set compute/zone us-east1-b



Task1: Create a bucket
----------------------

> You need to create a bucket for the storage of the photographs.

    export BUCKET_NAME=chomskie-20210417142
    gsutil mb gs://$BUCKET_NAME


Task 2: Create a pub/sub topic
------------------------------

> Create a Pub/Sub topic for the Cloud Function to send messages.

    export TOPIC=myTopic
    gcloud pubsub topics create $TOPIC


Task 3: Create the thumbnail Cloud Function
-------------------------------------------

> Create a Cloud Function that executes every time an object
> is created in the bucket you created in task 1.

Supplied code has been copied into `supplied_index.js` and `supplied_package.json`

    mkdir thumbnail_func
    cd thumbail_func
    
    cat ../supplied_index.js \
    sed -e "s/REPLACE_WITH_YOUR_TOPIC ID/$TOPIC/" > index.js

    cp ../supplied_package.json package.json

    
Create the cloud function.

For background, see
[Cloud Functions - Cloud Storage Tutorial](https://cloud.google.com/functions/docs/tutorials/storage)

    gcloud functions deploy thumbnail \
      --trigger-resource $BUCKET_NAME \
      --trigger-event google.storage.object.finalize \
      --runtime nodejs10
    
    gcloud functions describe thumbnail


Testing - grab a jpg and copy it into the bucket.

    wget https://storage.googleapis.com/cloud-training/gsp315/map.jpg
    
    gsutil cp map.jpg gs://$BUCKET_NAME
    
    gcloud functions logs read --limit 50



Task 4: Remove the previous cloud engineer
------------------------------------------

    gcloud projects get-iam-policy $GOOGLE_CLOUD_PROJECT

    gcloud projects remove-iam-policy-binding  $GOOGLE_CLOUD_PROJECT\
      --member="user:name@gmail.com" \
      --role="roles/iam.serviceAccountUser"

