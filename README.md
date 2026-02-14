# Resources

https://www.youtube.com/watch?v=AknbizcLq4w

# Continuous Integration (CI)

## How do we fix merge issues and test failures?

Let's start at the very beginning of the development workflow.  
How do we fix the problem of having a bunch of issues and failed tests every time we merge a feature branch into the main?  

Wouldn't it be more efficient if we ran those tests in the feature branch itself before trying to merge it?  
Why are we waiting for it to be merged to test if it breaks anything?  
Plus, if we have multiple feature branches, let's test each one individually before merging them into the main.  

Because when 3 different features are merged at the same time with large code changes, we won't even know which 
combination pieces had issues. Maybe one feature affects the other in some way...  
So, untangling the issue may be very difficult.  

Which is why we should run those tests on individual branches to find any bugs right before we merge.  
So developers working on those branches can fix their own code.  

Even better, instead of waiting until merge time to run those tests, let's run them on every commit.  
This way it will be easier to isolate the issue.  

Some developers build a feature locally for days, and then push the whole implementation at once.  
Instead, DevOps encourages developers to commit and push more often, so that smaller changes are tested more frequently.  

That is what CI is all about: committing smaller code changes more frequently and testing them right away.  
In other words: `Commit Small. Frequently. Test fast. Fix early.`  

## Tool suggestion: CodeRabbit

CodeRabbit analyzes your PRs (Pull Requests) automatically, detecting potential bugs, security vulnerabilities, and performance issues 
before they break your pipeline. So you can fix problems immediately instead of spending hours debugging some cryptic logs later.  

It doesn't try to replace human reviewers. It handles the initial screening so your team can focus on the more complex aspects of the code review.  
Think of it as an automated first check that catches the obvious issues, allowing human reviewers to spend more time on architecture and design decisions.  

The Pro plan costs 12$/developer/month if billed annually.

# Continuous Delivery (CD)

Now that we reduced the stress of testing before deployment using CI, let's zoom into the Deployment part.  

## Build automation tool

After merging into the main branch, we can use tools like **Jenkins** or **GitHub Actions** to run extensive tests against our code.  
If all these tests are successful, we can tell these tools to: 
- build our app into a Docker image,
- give it the next version tag,
- push it to the Docker registry (new artifact created)
- connect to the server/cluster (dev environment)
- run the Docker container (from the Docker image) inside the dev environment

If we created a new Deployment or Service (components for our K8s cluster), or we made configuration changes to the application, 
we can ask these same tools to apply those changes on the dev environment.  

So, all that logic of checking out the code locally, building a new Docker image for the next version of our app, updating the K8s deployment manifest file, 
connecting to the cluster, running `kubectl apply`... is automated in a **Jenkins** file script or **GitLab CI** or **GitHub Actions** pipeline file.  

Of course, setting up a CI/CD pipeline with such tools requires a few days of work.  
But it's worth the investment because it will serve us for years.  

## Dev - E2E Testing

What happens after we deploy to dev environment?  

Instead of waiting for release to do thorough end-to-end tests, testing all the integrations with databases and other services, 
we run these extensive tests inside our dev environment every time code changes get merged into the main branch.  

That part also requires writing a lot of automated tests for complex use cases.  

## Deploying to Staging environment (pre-production)

We tested our code before deploying to dev environment, then we ran E2E tests while our app was running in the dev environment.  
If our app passed those tests successfully, we can now deploy it to our staging environment.  

That workflow of deploying a release all the way to the staging (or testing) environment is called Continuous Delivery.  

## Deploying to production

Before releasing the new version of our app to production, we need to run some additional tests inside our staging environment:
- performance test
- compliance check
- security test

Very often, we don't want to automatically deploy to production.  
Sometimes, staging may be used by the dev team or PM or product owner to demo the new features first to higher decision makers.  
There, we might get useful input from them and then make some needed adjustments.  
So it's common to have a manual confirmation before deploying to prod.  

# Deployment strategies

No testing is 100% perfect, and bugs can still slip through.  
To further reduce the risk, we use deployment strategies such as:
- canary deployment:
  - roll out the new feature to 1% of users and observe for an hour
  - crank it up to 10% of users and observe for 2 hours
  - then 20% of users for a longer period of time
  - if nothing bad happens, let's switch to 100%
- blue-green deployment:
  - requires 2 identical prod environments
  - deploy new version to the green environment and keep running the current version inside the blue one
  - if the new deployment has an issue, we can easily switch the entire traffic back to the current version  


@19/21
