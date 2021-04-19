1. Create a project (using Vue.js + CLI)
Actually, you can use any kind of library or framework to deploy to GCP. This tutorial happens to use Vue.js as an example project because I was trying it out myself. I believe you can follow this tutorial too if you prefers using React or Angular.
If you’re not using Vue CLI, it’s time to. Personally, it’s just nicer to have a predefined project structure when you are just getting started. To read more about Vue CLI, you can visit their website.
npm install -g @vue/cli
Then, create a sample project using the CLI. You can substitue the project-name with your choosen project name.
vue create [project-name]
You can try to run npm run serve inside the directory to see that the project it created fine. I’ll just use the default Vue.js page, but do feel free to pause and explore Vue.js on your own.
2. Add YAML files for the setup
There will be two YAML file that we’re going to add to our root repository, cloudbuild.yaml and app.yaml.
cloudbuild.yaml configures the building of our Vue.js project in Cloud Build when we are pushing to master branch. You can adjust which branch will trigger deployment in step 8.
# cloudbuild.yaml
steps:
- name: node:10.15.1
  entrypoint: npm
  args: ["install"]
- name: node:10.15.1
  entrypoint: npm
  args: ["run", "build"]
- name: "gcr.io/cloud-builders/gcloud"
  args: ["app", "deploy"]
timeout: "1600s"
app.yaml configures App Engine’s settings. Here it defines our website’s entry point. As you noticed, dist directory is the result of Vue.js (and Angular) production build. For React projects, you can substitute it with build.
# app.yaml
runtime: nodejs10
handlers:
- url: /(.*\..+)$
  static_files: dist/\1
  upload: dist/(.*\..+)$
- url: /.*
  static_files: dist/index.html
  upload: dist/index.html
Props to Brian Young. I got the configuration files from his tutorial. It is the most complete and direct tutorial I have seen on this subject. However, I still feel that a more thorough tutorial is needed to clear up several things and I have added a few steps on my own in this tutorial.
3. Push the project to GitHub
This is a pretty simple step since you should already be familiar with GitHub. Since we have created a local directory, create a new Github repository for the existing repository. After creating a new repository, copy your HTTPS repository link.
Let us add the local repository to GitHub.
git init
git add .
git commit -m "init"
git remote add origin [github-repo-HTTPS-link]
git push -u origin master
4. (optional) Create a GitHub Action workflow
It is not necessary create a GitHub Action to achieve our goal. But it is good for continuous integration (CI), which I also encourage you to read more about. However, in this tutorial, I am including this because it is good to know that the Vue.js project will build successfully before we push to master.
In the GitHub repository, choose Actions tab.

Workflow options in GitHub’s Actions tab
As you can see, there are several options to choose from. But because we are deploying Vue.js project, we are going to choose Node.js workflow.
If you noticed too, there is also an option to build and deploy to Google Kubernetes Engine (GKE). Maybe I’ll dicuss it in another tutorial. Maybe.
After clicking on “Set up this workflow”, we are going to add a nodejs.yml file in .github/workflow. You don’t have to change anything in it. However, if you’d like to tweak it to suit your needs, go ahead and read its guide. After all that, start commit the nodejs.yml.

When GitHub Action is used, GitHub will automatically run checks on pull request. It is useful to check whether your pull request will not cause some error when building for production.
5. Mirror the GitHub repository to Cloud Source Repositories
Google Cloud gives a good tutorial on mirroring repositories for GitHub (and Bitbucket) in the following link.
Mirroring repositories | Cloud Source Repositories Documentation
Except as otherwise noted, the content of this page is licensed under the Creative Commons Attribution 4.0 License, and…
cloud.google.com

I will not write the tutorial here. Why, you might ask. Well, their documentation have described it better than I ever can. I feel redundant copying it here. If you are having trouble with this step, feel free to ask in the comments.
When you have successfully mirrored your GitHub account, it should show up in All Repositories tab.

In my example, I have mirrored my Github GCP Tutorial repository to my github-gcp-tutorial project.
6. Enable Cloud Build API and App Engine Admin API
Before we can use Cloud Build to deploy our app to App Engine, we need to enable it both. Google Cloud gives a short tutorial on it but I will guide you some additional steps here.
1. Open Cloud Build Settings page
2. Choose your project if it had not been choosen
3. Click on View API button

4. Choose Enable in the Cloud Build API page
5. Open App Engine API page and select your project
6. Choose Enable in the App Engine API page
5. Go to Cloud Build Settings page again
6. Enable App Engine like the following

Only App Engine is needed to be enabled.
7. Create an App Engine application
Because Cloud Build will build the repository to an App Engine application, we should create it first.
Go to App Engine tab in your GCP console
Click Create Application button
Choose your Region
Click Create App button
You will be asked the Language and Environment options which are optional. But I encourage to choose “Node.js” for the Language options so you will be given Node.js documentation links for App Engine in the next page.
8. Build trigger in Cloud Build
Google Cloud provides another wonderful tutorial on how to build a trigger in Cloud Build. This time, I will write it here with a (very) few addition to my own.
Open your Cloud Build Triggers page
Choose your project if it had not been choosen
Click +Create Trigger button
These are the following fields that you have to fill
Name: the name of the trigger. You can name it whatever you want, but because I am creating a trigger when I push to master, I’m naming it “push-master-branch”
Event: there are three kinds of event that you can choose: Push to a branch, Push a new tag, and Pull request (although only for GitHub). Since I am creating a trigger when I am pushing a commit, I choose “Push to a branch”
Source: if you have choosen “Push to a branch” in event, it will show Repository and Branch option. Since I have added the repository, I can choose it in the dropdown. Also, this is where you can choose which branch push will trigger the build. All the branches that you have pushed to Github will be shown in the dropdown. In this case, I choose “^master$”.
Build configuration: there should be nothing that you need to change. But to make sure, check if the input is like the following.

Build configuration option by default
There are several more options that you can change. I personally have not played it around but do tell me what they can do.
Conclusion
By the last step, you should be good to go. But to check if every is working as it is, you could manually run the trigger in Cloud Build Triggers page and choose Run trigger. If you have done the step correctly, the trigger should work smoothly and show “Successful” message in the History tab of Cloud Build.
To see your website, you can find its url in App Engine’s dashboard. Usually it follows the following format.
https://[project-id].[region-id].r.appspot.com
