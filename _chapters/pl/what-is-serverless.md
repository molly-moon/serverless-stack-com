---
layout: post
title: Czym jests serverless?
date: 2020-10-19 12:00:00
lang: pl
ref: what-is-serverless
description: Serverless odnosi się do aplikacji, w których zarządzanie oraz alokacja serwerów i zasobów jest całkowicie zarządzana przez dostawcę chmury. Rozliczenie kosztów opiera się na rzeczywistym zużyciu zasobów.
comments_id: what-is-serverless/27
---

Zazwyczaj budujemy i wdrażamy aplikacje internetowe, w których mamy pewien stopień kontroli nad żądaniami HTTP kierowanymi do naszego serwera. Nasza aplikacja działa na tym serwerze, a my odpowiadamy za udostępnianie i zarządzanie zasobami dla niej. Z tym podejściem wiąże się kilka problemów.

1. Jesteśmy obciążani kosztami utrzymania serwera nawet wtedy, gdy nie obsługujemy żadnych żądań. 

2. Jesteśmy odpowiedzialni za dostępność i utrzymanie serwera oraz wszystkich jego zasobów.

3. Jesteśmy również odpowiedzialni za stosowanie odpowiednich aktualizacji zabezpieczeń na serwerze. 

4. Wraz ze wzrostem obciążenia musimy zarządzać skalowaniem naszego serwera w górę. A co za tym idzie, musimy zarządzać skalowaniem w dół, gdy nie mamy tak dużego obciążenia.

W przypadku mniejszych firm i indywidualnych deweloperów może to się okazać trudne do wykonania. W rezultacie nie skupiamy się na zadaniach ważniejszych: budowaniu i utrzymaniu aplikacji. W większych organizacjach zajmuje się tym zespół ds. infrastruktury i zwykle nie jest to obowiązkiem samego dewelopera. Procesy związane z zarządzaniem serwerami mogą niestety spowolnić prace nad aplikacją, ponieważ nie możesz po prostu zbudować aplikacji bez współpracy z zespołem ds. infrastruktury, który pomoże Ci rozpocząć pracę. Będąc programistami szukaliśmy rozwiązania tego problemu, i właśnie tu pojawia się technologia serverless.

### Usługi obliczeniowe serverless

Usługi obliczeniowe serverless (w skrócie serverless), to an execution model where the cloud provider (AWS, Azure, or Google Cloud) is responsible for executing a piece of code by dynamically allocating the resources. And only charging for the amount of resources used to run the code. The code is typically run inside stateless containers that can be triggered by a variety of events including http requests, database events, queuing services, monitoring alerts, file uploads, scheduled events (cron jobs), etc. The code that is sent to the cloud provider for execution is usually in the form of a function. Hence serverless is sometimes referred to as _"Functions as a Service"_ or _"FaaS"_. Following are the FaaS offerings of the major cloud providers:

- AWS: [AWS Lambda](https://aws.amazon.com/lambda/)
- Microsoft Azure: [Azure Functions](https://azure.microsoft.com/en-us/services/functions/)
- Google Cloud: [Cloud Functions](https://cloud.google.com/functions/)

While serverless abstracts the underlying infrastructure away from the developer, servers are still involved in executing our functions.

Since your code is going to be executed as individual functions, there are a couple of things that we need to be aware of.

### Microservices

The biggest change that we are faced with while transitioning to a serverless world is that our application needs to be architectured in the form of functions. You might be used to deploying your application as a single Rails or Express monolith app. But in the serverless world you are typically required to adopt a more microservice based architecture. You can get around this by running your entire application inside a single function as a monolith and handling the routing yourself. But this isn't recommended since it is better to reduce the size of your functions. We'll talk about this below.

### Stateless Functions

Your functions are typically run inside secure (almost) stateless containers. This means that you won't be able to run code in your application server that executes long after an event has completed or uses a prior execution context to serve a request. You have to effectively assume that your function is invoked in a new container every single time.

There are some subtleties to this and we will discuss in the [What is AWS Lambda]({% link _chapters/what-is-aws-lambda.md %}) chapter.

### Cold Starts

Since your functions are run inside a container that is brought up on demand to respond to an event, there is some latency associated with it. This is referred to as a _Cold Start_. Your container might be kept around for a little while after your function has completed execution. If another event is triggered during this time it responds far more quickly and this is typically known as a _Warm Start_.

The duration of cold starts depends on the implementation of the specific cloud provider. On AWS Lambda it can range from anywhere between a few hundred milliseconds to a few seconds. It can depend on the runtime (or language) used, the size of the function (as a package), and of course the cloud provider in question. Cold starts have drastically improved over the years as cloud providers have gotten much better at optimizing for lower latency times.

Aside from optimizing your functions, you can use simple tricks like a separate scheduled function to invoke your function every few minutes to keep it warm. [Serverless Framework](https://serverless.com) which we are going to be using in this tutorial has a few plugins to [help keep your functions warm](https://github.com/FidelLimited/serverless-plugin-warmup).

Now that we have a good idea of serverless computing, let's take a deeper look at what a Lambda function is and how your code will be executed.
