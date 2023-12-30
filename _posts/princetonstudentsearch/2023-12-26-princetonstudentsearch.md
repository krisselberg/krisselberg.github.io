---
key: blog
title: LLM Princeton Student Search
date: "2023-12-26T12:00:00.000Z"
excerpt: "An NLP interface for OpenSearch to manage a database of Princeton University students and their contact information, integrating GPT-4 for query translation"
---

<div align="center">
    <img src="/assets/ptonstudentsearchhome.gif" alt="Princeton Student Search Home Screen" style="width: 75%; padding-bottom: 8px;"/>
</div>

## Architectural Decisions

In all honesty, this project was initially started to better understand the following technologies:

- NextJS + React (Frontend)
- OpenSearch
- AWS

In hindsight, these architectural decisions ended up being the most optimal for balancing speed and reliability. The integration of NextJS with React ensured fast page loads with server-side rendering and enabled a quick set up with create-next-app. I started building out an ExpressJS server for the API, but I stumbled across a nifty feature called [Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers) which allows for defining API routes within a NextJS application. This eliminated the additional complexity of managing a separate API server alongside the web app.

Using OpenSearch for text search may have been overkill for a small project like this, but it opens the door for more complex queries with its proximity search feature. This allows for use of a "slop" parameter that specifies how many positions apart matched terms can be while still considering the document a match. This is particularly useful in recruiting scenarios where someone may be looking for a student with "full-stack" experience, which many students may list as "fullstack" or "full stack".

Using the AWS OpenSearch Service helped with easy setup and scaling capabilities. I chose to use the "t3.small.search" instance type with one data node to minimize costs and defined a master user with fine-grained access controls for better security. In addition, deploying the NextJS web app to AWS through Elastic Beanstalk simplified communication with the OpenSearch service and provided easy scaling options. Internal traffic within AWS is also typically priced lower than traffic crossing to other networks.

Other technologies used include:

- Tailwind CSS (quick and easy styling)
- Firebase (quick and easy authentication)
- GPT-4 API (reliable parsing of natural language queries to OpenSearch queries)
  - Note: There was a drastic difference in performance when upgrading from gpt-3.5-turbo to gpt-4
- Docker (containerization for consistent development environments and easy deployment)

## Feature Highlights

There are two core functionalities of the app: "Search Students" and "Add Student". The "Add Student" feature is probably unnecessary for the main purpose of the app (improving search of Princeton students for recruiting/personal reasons), but it streamlines the process of populating the database. Here's how they work:

The "search" functionality works by taking a natural language query as input, parsing it with the GPT-4 API into a structured OpenSearch query format, executing that query against the OpenSearch service, and mapping the results to "cards" that a client can look through.

The "add student" functionality simply takes the input fields and forms a document creation request that is sent to the OpenSearch service where the new data is added to the existing dataset.

## Technical Challenges and Solutions

At first, I tried setting up the OpenSearch Service and AWS-hosted web app to communicate using an [Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) for its many advantages in privacy. This allows for the creation of a private, isolated section of the AWS cloud where one can launch AWS resources in a virtual network. This seemed perfect for my use case, but I found that the high pricing with enabling a VPC in a production environment to overwhelm any security advantages. For example, for private subnets hosting my web app, I was paying an hourly rate for a NAT gateway to access the Internet (for the GPT-4 API). Additionally, the OpenSearch Service Domain with multiple instances and required production-capable instance types was racking up my billing expenses. This set up added much more complexity than I was expecting with setting up route tables, public and private subnets, and security groups.

I opted for setting up a master user with fine-grained access control on a "Dev/test" OpenSearch Service Domain using the cheapest "t3.small.search" instance type with a public IP. This allows me to easily send requests using the master user credentials from my web app and get something shipped quickly. If traffic and load increases, I may upgrade to the more secure VPC and more capable production-level models, but these incurred unnecessary costs during development.

## Future Roadmap

For now, I only have six students in the OpenSearch database. The most obvious next step is getting more data. I plan on doing this by building a scraper that cross references the public student directory at Princeton with public LinkedIn profiles and summarizes profile data into the description field using the GPT-4 API. The more comprehensive the database, the more useful a platform like this would be for recruiters to find students with specific experience. With the addition of more students, a use case is developed for further filtering capabilities on search results. These could be based on criteria such as graduation year, major, skills, GPA, and extracurricular activities, enhancing the precision and relevance of search results for various recruiting needs.

However, these product improvements are less important when compared to the need for validating the prototype. I plan on meeting with the Center for Career Development at Princeton University (and/or other career centers at universities) to see how I can adapt this project to something more suitable for their needs. For example, I can see the product pivoting to an alumni search platform if requested.

Check out the [GitHub repo](https://github.com/krisselberg/princetonstudentsearch) and the [working demo](http://princetonstudentsearch-env.eba-eb7whwcc.us-east-1.elasticbeanstalk.com/) (contact me for credentials and I can make you an account if you want to play around with it)!

<div align="center">
    <img src="/assets/ptonstudentsearchlogo.jpg" alt="Princeton Student Search Logo" style="width: 50%; padding-bottom: 8px;"/>
</div>
<div align="center">
    <em>Logo generated by ChatGPT's DALL-E</em>
</div>
