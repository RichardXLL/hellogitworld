Deploying GitHub repository to Google Cloud Platform

1. Imported a Repo called hellogitworld

2. Add YAML files in the Repo 

For configuration files and in applications where data is being stored or transmitted.

# cloudbuild.yaml

# app.yaml


3. Add quickstart.sh


5. Mirror the GitHub repository to Cloud Source Repositories


6. Enable Cloud Build API and App Engine Admin API


7. Create an App Engine application


8. Build trigger in Cloud Build


Choose your project if it had not been choosen
Click +Create Trigger button
These are the following fields that you have to fill

“Push to a branch”
choose “^master$”.

In Cloud Build Triggers page and choose Run trigger. 
The trigger worked smoothly and show “Successful” message in the History tab of Cloud Build.


