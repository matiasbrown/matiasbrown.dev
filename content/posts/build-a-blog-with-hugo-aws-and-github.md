---
title: "Build a Blog With Hugo, Aws and Github"
date: 2023-11-13T13:28:24-03:00
draft: false
description: ""
subtitle: ""
header_img: "img/home-bg.jpeg"
toc: true
tags: ['aws', 'hugo', 'github']
categories: ['aws', 'cloud']
series: ['AWS guides']
comment: true
---

Introducing a seamless static website deployment with Hugo, AWS S3, and GitLab

A simple way to host a website on AWS is by putting the files in an S3 bucket and delivering them quickly using CloudFront. This setup ensures that when someone visits the website, CloudFront will show them the file from its storage if it's there. If not, it quickly gets it from S3.

To keep the website up-to-date, we use a powerful tool called CodePipeline. This tool helps us make changes to the website and get them online smoothly. 

### How it works?

1. **Make a Change**: When you want to update your website, just make the changes and save them in GitLab.
2. **Let CodePipeline Do the Work**: CodePipeline notices your changes in GitLab and brings them into the system.
3. **Build with CodeBuild**: CodeBuild helps put together all the code and build your website with Hugo.
4. **Update the Website in S3**: Once everything is ready, CodePipeline moves the updated website files into AWS S3.
   
This way, whenever you make changes to your website and save them in GitLab, the whole process of updating your site online is taken care of automagically.

### Why should you consider this combination?

1. **Lightning-Fast Speed**: With Hugo, an extremely fast static site generator, speed is a standout feature. Content is generated in advance, ensuring users experience ultra-fast loading times. This not only enhances the user experience but also positively impacts SEO.
2. **Robust Security**: By utilizing a static blog, you significantly reduce attack surfaces. No exposed databases or server-side logic. AWS S3, your storage haven, allows precise access policies, while Hugo naturally generates secure static pages. Less security concerns, more peace of mind.
3. **Effortless Scalability**: AWS S3, being a highly scalable storage service, allows you to handle any amount of content effortlessly. As your blog grows, S3's inherent scalability ensures it always meets demand.
4. **Efficient Automation**: AWS CodePipeline and CodeBuild, along with GitLab, enable you to automate the build and deployment process of your static blog. Each time you make a change to your repository, the pipeline automatically takes care of building and updating your blog.
5. **Transparent Collaboration**: GitLab acts as the collaborative core, facilitating source code management and team coordination. Integrations with AWS make the development and deployment workflow a seamless experience.
6. **Low and Transparent Costs**: By adopting a static approach, you eliminate the need for continuously running servers. AWS S3 offers a transparent pricing structure—pay only for what you use, and data transfer costs are minimal. It's a resource-efficient and financially attractive solution.
7. **Customization and Flexibility**: Hugo gives you complete control over the design and structure of your blog. You can customize the visual appearance and user experience according to your preferences. The flexibility provided by Hugo, combined with AWS scalability, allows your blog to grow and evolve as needed.


### Step-by-Step Guide

The following is a detailed list of steps (although somewhat long as well) that will allow you to have a site with high availability, efficiency, and dynamism. You can find the source code for this blog at [https://github.com/matiasbrown/matiasbrown.dev](https://github.com/matiasbrown/matiasbrown.dev)

#### Setting Up Route 53 Hosted Zone

Begin by creating a hosted zone in Route 53 for your domain name. In my case matiasbrown.dev.

![Route 53 getting started](/img/posts/build-a-blog-with-hugo-aws-and-github/00-route-53-01.jpg)
![Create a Route 53 hosted zone](/img/posts/build-a-blog-with-hugo-aws-and-github/00-route-53-02.jpg)

#### Creating an S3 Bucket
Establish an S3 bucket to store your blog's content.

The ACLs should be disabled.

![Create an S3 bucket for the blog's content](/img/posts/build-a-blog-with-hugo-aws-and-github/01-s3-01.jpg)

The public access must be blocked.

![Create an S3 bucket for the blog's content public access](/img/posts/build-a-blog-with-hugo-aws-and-github/01-s3-02.jpg)

Add your custom tags and choose the encryption method.

![Create an S3 bucket for the blog's content tags and encryption](/img/posts/build-a-blog-with-hugo-aws-and-github/01-s3-03.jpg)

#### Building a CodePipeline
Develop a CodePipeline to automate the build and deployment process for your site. For more information about CodePipeline go to [AWS docs](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html)

![Create CodePipeline](/img/posts/build-a-blog-with-hugo-aws-and-github/02-pipeline-01.jpg)
![Create CodePipeline rest](/img/posts/build-a-blog-with-hugo-aws-and-github/02-pipeline-02.jpg)

#### Source Stage and GitLab Integration
Forge a connection with GitLab to seamlessly manage and coordinate your code changes.

For the source provider i'm choosing GitHub (version 2) that use GitHub Apps to access your repository.

After creating or selecting the connection with GitHub (I explain how to create it below), you need to select the repository. Also, the way in which the pipeline execution will be triggered. In my case, I chose to have it triggered when I push a change to the main branch.

![Integrate source with gitlab](/img/posts/build-a-blog-with-hugo-aws-and-github/03-source-01.jpg)
![Integrate source with gitlab rest](/img/posts/build-a-blog-with-hugo-aws-and-github/03-source-02.jpg)

Create GitLab connection with the "Connect to GitHub" button or, if you already have one, choose it.

![Integrate source with gitlab create connection](/img/posts/build-a-blog-with-hugo-aws-and-github/03-source-03.jpg)

After you click the "Connect to GitHub" button, you can access to the "Install a new app" button.

![Integrate source with gitlab install new app](/img/posts/build-a-blog-with-hugo-aws-and-github/03-source-04.jpg)

Now, you must log in to your GitHub account and authorize AWS Connector for GitHub to access your repository.

![Integrate source with gitlab allow AWS Connector for GitHub](/img/posts/build-a-blog-with-hugo-aws-and-github/03-source-05.jpg)

You can install the app in all your repositories or some of them. You must install the app in the repository of your site.

![Integrate source with gitlab Install the AWS Connector for Github to your repository](/img/posts/build-a-blog-with-hugo-aws-and-github/03-source-06.jpg)

Now, the connection is ready and the created app code is selected in the GitHub Apps field.

![Integrate source with gitlab finish the connection configuration](/img/posts/build-a-blog-with-hugo-aws-and-github/03-source-07.jpg)


#### AWS CodeBuild for Site Construction
Set up an AWS CodeBuild project to compile and construct your blog site. For more information about CodeBuild go to [AWS docs](https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html)

![Set up an AWS CodeBuild project](/img/posts/build-a-blog-with-hugo-aws-and-github/04-build-01.jpg)

Create the AWS CodeBuild project. 

![Set up an AWS CodeBuild project - Creation](/img/posts/build-a-blog-with-hugo-aws-and-github/04-build-02.jpg)

Choose the environment for building the project. In my case, I need it to be an AWS-managed image in EC2 running Ubuntu.

![Set up an AWS CodeBuild project - Creation](/img/posts/build-a-blog-with-hugo-aws-and-github/04-build-03.jpg)

Select the option "Use buildspec file". This file contains the specifications and steps for building the project. [You can find it in the project's code](https://github.com/matiasbrown/matiasbrown.dev/blob/main/buildspec.yml).

![Set up an AWS CodeBuild project - Creation](/img/posts/build-a-blog-with-hugo-aws-and-github/04-build-04.jpg)

I you want, you can enable the CloudWatch logs option to store the build project logs.

![Set up an AWS CodeBuild project - Creation](/img/posts/build-a-blog-with-hugo-aws-and-github/04-build-05.jpg)

#### Deploy the Website Built in S3
Finally, choose your website S3 bucket in the deploy stage.

![Deploy the project in S3](/img/posts/build-a-blog-with-hugo-aws-and-github/05-deploy-01.jpg)

#### Enhancing CodeBuild Role with Inline Policy
Elevate the capabilities of the CodeBuild role by adding an inline policy for GitLab connection.

If you need to run git commands in your project build and access your connection with GitLab, you need to add the permission to use the connection to the role that the CodeBuild project has. You can access the CodeBuild project role from the Build details, Environment section.

![Search the CodeBuild project service role](/img/posts/build-a-blog-with-hugo-aws-and-github/06-share-connection-01.jpg)

Attach an inline policy to the role to share the gitlab connection acces to the CodeBuild project allowing the action ```codestar-connections:UseConnection```.

![Add an inline policy to share gitlab connection access](/img/posts/build-a-blog-with-hugo-aws-and-github/06-share-connection-02.jpg)

Replace the shared connections ARN in the policy:

{{< gist matiasbrown 6b2e3cde09fb90070a25754ec38b5243 policy.json >}}

#### SSL Certificate for CloudFront
We need to create an SSL Certificate to secure your CloudFront distribution. The certificate must be created in the region us-east-1 (N. Virginia)

![Request a public SSL certificate](/img/posts/build-a-blog-with-hugo-aws-and-github/07-ssl-cloudfront-01.jpg)

Add your domains for the certificate. If you add a domain with a wildcard (*) you could use this certificate in all your subdomains.

![Request a public SSL certificate for your domain](/img/posts/build-a-blog-with-hugo-aws-and-github/07-ssl-cloudfront-02.jpg)
![Request a public SSL certificate tags](/img/posts/build-a-blog-with-hugo-aws-and-github/07-ssl-cloudfront-03.jpg)

After you request the certificate, you need to validate the domain. You can create the route 53 validation records from the certificate details page.

![Request a public SSL certificate create route 53 validation records](/img/posts/build-a-blog-with-hugo-aws-and-github/07-ssl-cloudfront-04.jpg)

This creates the needed record in the hosted zone.

![Request a public SSL certificate create route 53 validation records](/img/posts/build-a-blog-with-hugo-aws-and-github/07-ssl-cloudfront-05.jpg)

#### Deploying CloudFront Distribution
Establish a CloudFront distribution to efficiently access your site hosted on S3.

Select the domain of your S3 bucker in the Origin domain field and create a new control setting.
![Create a CloudFront distribution](/img/posts/build-a-blog-with-hugo-aws-and-github/08-cloudfront-distribution-01.jpg)

I choosed "Redirect HTTP to HTTPS" as viewer protocol policy to redirect all the not secured request to HTTPS.
![Create a CloudFront distribution](/img/posts/build-a-blog-with-hugo-aws-and-github/08-cloudfront-distribution-02.jpg)
![Create a CloudFront distribution](/img/posts/build-a-blog-with-hugo-aws-and-github/08-cloudfront-distribution-03.jpg)
![Create a CloudFront distribution](/img/posts/build-a-blog-with-hugo-aws-and-github/08-cloudfront-distribution-04.jpg)

Fill your alternate domain names of your site and select the created SSL certificate. Enter the root object file name for the root URL.
![Create a CloudFront distribution](/img/posts/build-a-blog-with-hugo-aws-and-github/08-cloudfront-distribution-05.jpg)
![Create a CloudFront distribution](/img/posts/build-a-blog-with-hugo-aws-and-github/08-cloudfront-distribution-06.jpg)

#### Adjusting Bucket Permissions
Modify S3 bucket permissions to allow access by the CloudFront distribution.

After creating the CloudFront distribution, you can copy the S3 bucket policy to allow access to the S3 objects from the CloudFront distribution.

![S3 bucket permissions](/img/posts/build-a-blog-with-hugo-aws-and-github/09-s3-permissions-01.jpg)

Go to the S3 bucket permissions and edit the bucket policy.

![S3 bucket permissions](/img/posts/build-a-blog-with-hugo-aws-and-github/09-s3-permissions-02.jpg)

Edit the bucket policy and allow objects access permission to your CloudFront distribution ARN.

![S3 bucket permissions](/img/posts/build-a-blog-with-hugo-aws-and-github/09-s3-permissions-03.jpg)

Here is the S3 bucket permission. Replace AWS_ACCOUNT with your account number and DISTRIBUTION_ID with your CloudFront distribution ID.

{{< gist matiasbrown 6b2e3cde09fb90070a25754ec38b5243 s3-bucket-policy-cloudfront.json >}}

#### Create the Route 53 alias record to redirect to your CloudFront distribution
You need to create an alias record in your Route 53 hosted zone to redirect from your domain to the CloudFront distribution domain.

![CloudFront route53 record](/img/posts/build-a-blog-with-hugo-aws-and-github/10-cloudfront-redirect-record-01.jpg)

#### CloudFront Function for Dynamic index.html

Because we are serving the site through CloudFront and an S3 bucket, requests without index.html throw an error because index.html is not returned as the root object for each URI.

![Access denied because index.html not exists](/img/posts/build-a-blog-with-hugo-aws-and-github/11-index-html-01.jpg)

To solve this problem you need to create and associate a CloudFront function to your CloudFront distribution to automatically append index.html to URLs as needed.

![CloudFront function for dynamic index.html](/img/posts/build-a-blog-with-hugo-aws-and-github/11-index-html-02.jpg)

This is the code of the function that handles the request and rewrites the url to append index.html if it's neccesary. Source: [https://github.com/aws-samples](https://github.com/aws-samples/amazon-cloudfront-functions/blob/main/url-rewrite-single-page-apps/index.js)

{{< gist matiasbrown 6b2e3cde09fb90070a25754ec38b5243 RewriteDefaultIndexRequest.js >}}

Add the code to the Build section, Save the changes and then publish the version.
![CloudFront function for dynamic index.html](/img/posts/build-a-blog-with-hugo-aws-and-github/11-index-html-03.jpg)

Finally, you need to Add an association to the CloudFront distribution.

![CloudFront function for dynamic index.html](/img/posts/build-a-blog-with-hugo-aws-and-github/11-index-html-04.jpg)
![CloudFront function for dynamic index.html](/img/posts/build-a-blog-with-hugo-aws-and-github/11-index-html-05.jpg)

#### Pipeline Integration for CloudFront Invalidation
Integrate CloudFront invalidation into your CodePipeline , using a lambda function to ensure immediate updates upon site publication.

The last step we need to take is to add a call to a Lambda function to our CodePipeline that invalidates the CloudFront cache after the deploy, so that every time we update our site, the new content is available immediately and the old cached content is not displayed.

Create the Lambda function

![Ivalidate CloudFront cache using a lambda function](/img/posts/build-a-blog-with-hugo-aws-and-github/12-invalidate-cache-01.jpg)

The following code uses the AWS Python SDK, ```boto3```, to:
* Create a CloudFront invalidation
* Catch any exceptions and inform CodePipeline that the job failed
* Otherwise, inform CodePipeline that the job succeeded.
  
{{< gist matiasbrown 6b2e3cde09fb90070a25754ec38b5243 InvalidateCloudFrontDistributionCache.py >}}

Deploy the Lambda function code
![Ivalidate CloudFront cache using a lambda function](/img/posts/build-a-blog-with-hugo-aws-and-github/12-invalidate-cache-02.jpg)

Under "Configuration" and then "Permissions", click on the lambda function execution role name to go to it's details page.
![Ivalidate CloudFront cache using a lambda function](/img/posts/build-a-blog-with-hugo-aws-and-github/12-invalidate-cache-03.jpg)

Click to the policy assigned to the lambda function execution role.
![Ivalidate CloudFront cache using a lambda function](/img/posts/build-a-blog-with-hugo-aws-and-github/12-invalidate-cache-04.jpg)

Then, Edit the policy to add access to CodePipeline and CloudFront with the following statement.

{{< highlight json "style=emacs" >}}
{
    "Effect": "Allow",
    "Action": [
        "codepipeline:PutJobFailureResult",
        "codepipeline:PutJobSuccessResult",
        "cloudfront:CreateInvalidation"
    ],
    "Resource": "*"
}
{{< /highlight >}}

Save the policy changes.

![Ivalidate CloudFront cache using a lambda function](/img/posts/build-a-blog-with-hugo-aws-and-github/12-invalidate-cache-05.jpg)

Now, you need to go to the CodePipeline, edit it, add a new stage called Invalidate, and then add an Action group.

You also need to pass something in the "User parameters" field. These parameters specify which CloudFront distribution to invalidate and which paths to invalidate within the distribution. To invalidate everything, specify "/*". This value should be JSON encoded, and will look something like:

{{< highlight json "style=emacs" >}}
{"distributionId": "string", "objectPaths": ["/*"]}
{{< /highlight >}}

![Ivalidate CloudFront cache using a lambda function](/img/posts/build-a-blog-with-hugo-aws-and-github/12-invalidate-cache-06.jpg)

Finally Save the changes and test it.

-----------------

### Conclusion

So, there you have it! We've explored the magic of building and deploying a static website with the dynamic trio of Hugo, AWS S3, and GitLab. This combination guarantees a lightning-fast, secure, scalable, and cost-effective digital home for your content, all while streamlining your workflow with seamless automation and transparent collaboration.

Whether you're a seasoned blogger or a tech enthusiast dipping your toes into the digital ocean, this setup provides a robust foundation for your online presence. Take advantage of Hugo's customization potential to craft a unique identity for your website, leverage AWS's boundless resources to accommodate your growth, and enjoy the effortless workflow powered by GitLab's integrations.