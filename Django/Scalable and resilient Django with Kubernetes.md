原文：[Scalable and resilient Django with Kubernetes](https://harishnarayanan.org/writing/kubernetes-django/)

---

如果事情如你预想般工作，那么在你的webapp的生命周期中，它将会有服务于大量用户的时候。当事情已经走到了这一步，那么如果你已经将你的webapp架构成其规模可以优雅的满足这种负荷，同时对于底层计算资源的任意故障具有弹性，这将是很理想的。

这篇文章是关于你可以如何使用[Docker容器](https://www.docker.com/what-docker)以及[Kubernetes](http://kubernetes.io/)来帮助你的[Django](https://www.djangoproject.com) webapp来达到这些架构目标。虽然它在理论和理念上涉猎不多，但是它确实逐步完善一个[具体的例子](#practical-example-on-google-container-engine:d4c663f7b7e5088d61b55e9f2c9602ed)，以帮助巩固概念。

## 注意事项

在我们深入之前，我想指出，在这篇文章中表达的想法与Django没有什么特别的关系。我选择Django作为例子，只是单纯因为它是一个我熟悉的流行框架。对其他软件栈而言，重新利用这些原则是直截了当的。

我还想指出，这篇文章涉及了许多发展中的作品，它们中的一些相当不成熟。如果在你的webapp的生命周期的当前阶段中，你可以避免这种复杂度，那么_你应该避免_。相反，集中精力更好的理解用户的问题，以及测试你的应用是否解决了这些问题。除非足够多的人经常使用你的应用，否则没有人会知道或者抱怨你把你的应用运行在一个单一脆弱的服务器上。

_没有人。_

有了这样的方式，那么就让我们开始吧！

## 传统的基于虚拟机（VM）的部署存在有些问题

让我们来想象下，你正在做一个Django web应用，它以一种相当标准的形式进行布局：你所有的应用数据都保存在PostgreSQL服务器上。应用本身是用Django风格的Python编写的，并使用Gunicorn应用服务器。而在这一切之前，你使用NGINX web服务器，它即作为反向代理，又作为静态内容服务器。

  ![Layout of a non-trivial Django application.](/images/writing/kubernetes-django/standard-django-application.svg)
  <figcaption>Layout of a non-trivial Django application.</figcaption>

当你第一次开始了你的应用，并且只有少量用户时，它可以完美的将所有东东都运行在一台服务器上。所以你把应用跑在你[最喜欢的云服务提供商](https://m.do.co/c/e3559ea013de)在，启动一个VPS来运行Debian或其他什么操作系统，并在同一台机器上安装所有这些软件。


  ![All pieces making up the app on a single machine.](/images/writing/kubernetes-django/all-in-one-server.svg)
  <figcaption>All pieces making up the app on a single machine.</figcaption>

然后，随着你的应用开始变得受欢迎，你开始进行扩展工作。搜寻，你遵循简单的方法，简单的提供越来越大的单一机器来运行你应用。这就是所谓的_垂直扩展(vertical scaling)_，它行之有效，知道应用拥有了上千个用户。

接着，你的应用变得更受欢迎。

现在，你意识到，如果你分开组成你应用的组件，然后将它们放在不同的机器上，那么你就可以独立地扩展组件。这意味着，例如，你可以运行Django应用的多个实例（称为_水平扩展(horizontal scaling)_）来处理不断增长的用户群，同时继续把你的PostgreSQL服务器运行在唯一一个（但可能日益强大的）机器上。


  ![Running many instances of the app, talking to a single database.](/images/writing/kubernetes-django/on-separate-servers.svg)
  <figcaption>Running many instances of the app, talking to a single database.</figcaption>

其实，这是一个相当不错的部署方案(并且它的基本理念是我们今天在我的[日常工作](https://edgefolio.com/company/)中实践的基础，使用[Ansible](https://www.ansible.com)来设置服务器)，但它还有一些不便之处：

1.  为每个组件建立并保持最新的服务器是烦人的。这不是你想考虑的关于服务器的问题。

2.  通常，你拥有较差的资源利用率，因为每一个组件都不能有效地使用它所运行在的服务必须提供的所有资源。这主要是因为你通常为高峰负载进行安装，而不是平均负载。

3.  如果你试图通过在同个机器上运行多个组件（例如，应用和数据库）来解决(2)，那么就没有办法阻止组件之间的资源抢占。例如，在一个给定服务器上的匮乏资源隔离。

* * *

所以，如果我们能够将我们的关注点从管理服务器转移到简单在计算资源的集合上运行我们的应用的组件呢？此外，如果这些组件之间很好的相互隔离，并且有效地利用它们所拥有的资源呢？

然后，我们的部署图可能看起来更像下面这样，其中，我们关心的主要组件（应用组件）以橙色显示。组件运行的实际节点（物理机或虚拟机）在视觉上弱化了，因为我们不关心细节。并且，我们相信我们的基本计算基础架构为我们提供了一些基本功能，例如持久性存储和负载平衡器（以绿色显示）这些任何较重的web应用的常用功能。


  ![The application running on an abstract collection of resources.](/images/writing/kubernetes-django/scheduled-on-cluster.svg)
  <figcaption>The application running on an abstract collection of resources.</figcaption>

这种理念的转变 — [从 _管理服务器_ 到简单理想地 _运行我们应用的组件_](http://queue.acm.org/detail.cfm?id=2898444) — 恰恰是容器技术，例如[Docker](https://www.docker.com/)，和集群业务流程框架，例如[Kubernetes](http://kubernetes.io/)，所要提供的。而在实际的[下面的例子](#practical-example-on-google-container-engine:d4c663f7b7e5088d61b55e9f2c9602ed)中，我们将看到这些工具如何让我们能够轻松地重新创建上面显示的理想部署方案。

## 所以，Docker和Kubernetes是如何行之有效的呢？

你可以通俗地把[Docker](https://www.docker.com/)容器当做你的应用的[fat静态二进制文件](https://en.wikipedia.org/wiki/Static_library)。它们捆绑你的应用代码，底层库，以及你的应用程序需要运行的所有东东到一个便捷的包里，这个包可以直接的在Linux内核上的一个薄层之上运行。这在实践中意味着，你可以获得一个你已经构建了一次的容器，然后把它运行在不同版本的Linux发行版本上，或者完全不同的Linux发行版本上。所有这一切都应该无缝工作。

因此，通过形成一个可构建、测试已经随处运行的基本单元，容器将你的关注层次提高到操作系统的细节上，运行你关注自己的应用。容器还提供资源隔离，意味着如果其中两个并排运行，那么每一个都只能看到它应该看到的东东，以及做它应该做的事。

这意味着我们的部署之旅现在可以大略分成两个步骤。第一个步骤是得到我们的应用的不同组件，然后将它们打包到容器中。第二步是在我们的计算资源之上运行它们 —— 利用底层计算原语，例如_负载均衡器_，并确保容器正确联网。

这里，第二步就是[Kubernetes](http://kubernetes.io/docs/whatisk8s/)的用武之地。

Kubernetes是一个用于管理集群和部署“容器化”应用的开源系统。Kubernetes抽象（你的云供应商或者本地集群的）底层硬件，并提供一个简单的API以允许你容易地控制它。你向这个API发送一些声明式状态，例如：“我想要我的Django应用容器的三个副本运行在负载均衡器之后，拜托了”，然后，它可以确保在你的集群的节点上安排适当的容器。另外，它监控状态，并确保维持此状态，允许其对系统中的任意修改保持健壮。例如，这意味着，如果一个容器因为节点内存耗尽而被过早关闭，Kubernetes会注意到这点，并确保重新启动其他位置的另一个副本。

Kubernetes通过位于集群的每个节点上的代理来工作。这些代理允许一些行为，例如运行Docker容器 (_docker守护进程_)，确保维持需要的状态 (_kubelet_)，以及容器可以彼此交流 (_kube-proxy_)。这些代理监听及与一个集中的API服务器同步，以确保系统处于期望的状态。


  ![A simplified look at Kubernetes](/images/writing/kubernetes-django/kubernetes-architecture.svg)
  <figcaption>A simplified look at Kubernetes' architecture.</figcaption>

Kubernetes API暴露了集群配置资源集合，我们可以修改以表达我们希望我们的集群所处的状态。该API提供给了一个标准的REST接口，允许我们以以多种方式与它进行交互。在即将到来的例子中，我们将使用一个瘦命令行客户端，名为`kubectl`，来与API服务器进行通信。

虽然该API[提供大量原语](http://kubernetes.io/kubernetes/third_party/swagger-ui/)以供使用，但是这里还是有一些对于我们今天的例子重要的东西：

*   **Pods** are a collection of closely coupled containers that are
scheduled together on the same node, allowing them to share volumes
and a local network. They are the smallest units that can be
deployed within a Kubernetes cluster.

*   **Labels** are arbitrary key/value pairs (e.g. `name: app` or
`stage: production`) associated with Kubernetes resources. They
allow for an easy way to select and organise sets of resources.

*   **Replication Controllers** ensure that a specified number of pods
(of a specific kind) are running at any given time. They group pods
via labels.

*   **Services** offer a logical grouping of a set of pods that perform
the same function. By providing a persistent name, IP address or
port for this set of pods, they offer service discovery and load
balancing.

If this all seems a bit too abstract at the moment, do not fret. We’re
now going to jump into an example that demonstrates how these bits
work in practice to help us deploy our Django app.

## 谷歌容器引擎（Google Container Engine）上的应用实例

The example application that we’re going to be focusing on is a simple
blog application.


  ![Sample blog app following the Django Girls Tutorial.](/images/writing/kubernetes-django/django-girls-blog-screenshot.png)
  <figcaption>Sample blog app following the Django Girls Tutorial.</figcaption>

While this is a very basic example, it contains all the necessary
pieces we need to see the ideas we’ve discussed in practice. Over the
course of this example, we’re going to get access to a cluster
controlled by Kubernetes, split our blog application into separate
Docker containers, and deploy them using Kubernetes. The final result
matches the idealised diagram introduced earlier.


  ![The application running on an abstract collection of resources.](/images/writing/kubernetes-django/scheduled-on-cluster.svg)
  <figcaption>The application running on an abstract collection of resources.</figcaption>

And once we have things up and running, we’ll play around with the
Kubernetes API to do different things, such as scaling your app,
observe how it heals from failures, and learn how you can upgrade one
version of your Django app to another with no downtime.

### Preliminary steps

1.  Fetch the source code for this example.
```
    git clone https://github.com/hnarayanan/kubernetes-django.git
```
2.  [Install Docker](https://docs.docker.com/engine/installation/).
3.  Take a look at and get a feel for the [example Django
    application](https://github.com/hnarayanan/kubernetes-django/tree/master/containers/app) used in this repository. It is a simple blog
    that’s built following the excellent [Django Girls
    Tutorial](http://tutorial.djangogirls.org).
4.  [Setup a cluster managed by Kubernetes](http://kubernetes.io/docs/getting-started-guides/). The
    effort required to do this can be substantial, so one easy way to get
    started is to sign up (for free) on Google Cloud Platform and use a
    managed version of Kubernetes called [Google Container Engine](https://cloud.google.com/container-engine/)
    (GKE).

    1.  Create an account on Google Cloud Platform and update your
    billing information.
    2.  Install the [command line interface](https://cloud.google.com/sdk/).
    3.  Create a project (that we’ll refer to henceforth as
    `$GCP_PROJECT`) using the web interface.
    4.  Now, we’re ready to set some basic configuration.
    ```
    gcloud config set project $GCP_PROJECT
    gcloud config set compute/zone europe-west1-d
    ```
    5.  Then we create the cluster itself.
    ```
    gcloud container clusters create demo
    gcloud container clusters list
    ```
    6.  Finally, we configure `kubectl` to talk to the cluster.
    ```
    gcloud container clusters get-credentials demo
    kubectl get nodes
    ```
### 创建及发布Docker容器

For this example, we’ll be using [Docker Hub](https://hub.docker.com/)
to host and deliver our containers. And since we’re not working with
any sensitive information, we’ll expose these containers to the
public.

#### PostgreSQL

Build the container, remembering to use your own username on Docker
Hub instead of `hnarayanan`:
```sh
cd containers/database
docker build -t hnarayanan/postgresql:9.5 .
```

You can check it out locally if you want:
```sh
docker run --name database -e POSTGRES_DB=app_db -e POSTGRES_PASSWORD=app_db_pw -e POSTGRES_USER=app_db_user -d hnarayanan/postgresql:9.5
# Echoes $PROCESS_ID to the screen
docker exec -i -t $PROCESS_ID bash
```

Push it to a repository:
```sh
docker login
docker push hnarayanan/postgresql:9.5
```
#### 在Gunicorn中运行Django app

Build the container:
```sh
cd containers/app
docker build -t hnarayanan/djangogirls-app:1.2-orange .
```

Push it to a repository:

` docker push hnarayanan/djangogirls-app:1.2-orange`

We’re going to see how to perform rolling updates later in this
example. For this, let’s create an alternative version of our app that
simply has a different header colour, build a new container app and
push that too to the container repository.
```sh
cd containers/app
emacs blog/templates/blog/base.html

# Add the following just before the closing </head> tag
    <style>
      .page-header {
        background-color: #ac4142;
      }
    </style>

docker build -t hnarayanan/djangogirls-app:1.2-maroon .
docker push hnarayanan/djangogirls-app:1.2-maroon`
```

### 部署这些容器到Kubernetes集群

#### PostgreSQL

Even though our application only requires a single PostgreSQL instance
running, we still run it under a (pod) replication controller. This
way, we have a service that monitors our database pod and ensures that
one instance is running even if something weird happens, such as the
underlying node fails.
```sh
cd  kubernetes/database
kubectl create -f replication-controller.yaml

kubectl get rc
kubectl get pods

kubectl describe pod <pod-id>
kubectl logs <pod-id>
```

Now we start a service to point to the pod.
```sh
cd  kubernetes/database
kubectl create -f service.yaml

kubectl get svc
kubectl describe svc database
```

#### 在Gunicorn中运行Django app

We begin with three app pods (copies of the orange app container)
talking to the single database.
```sh
cd kubernetes/app
kubectl create -f replication-controller-orange.yaml
kubectl get pods

kubectl describe pod <pod-id>
kubectl logs <pod-id>
```

Then we start a service to point to the pod. This is a load-balancer
with an external IP so we can access the site.
```sh
cd kubernetes/app
kubectl create -f service.yaml
kubectl get svc
```

Before we access the website using the external IP presented by
`kubectl get svc`, we need to do a few things:

1.  Perform initial migrations:
`kubectl exec <some-app-orange-pod-id> -- python /app/manage.py migrate` 

2.  Create an intial user for the blog:
`kubectl exec -it <some-app-orange-pod-id> -- python /app/manage.py createsuperuser`

3.  Have a CDN host static files since we don’t want to use Gunicorn
for serving these. This demo uses Google Cloud storage, but you’re
free to use whatever you want. Just make sure `STATIC_URL` in
`containers/app/mysite/settings.py` reflects where the files are.
```sh
gsutil mb gs://demo-assets
gsutil defacl set public-read gs://demo-assets
cd django-k8s/containers/app
virtualenv --distribute --no-site-packages venv
source venv/bin/activate
pip install Django==1.9.5
export DATABASE_ENGINE='django.db.backends.sqlite3'
./manage.py collectstatic --noinput
gsutil -m cp -r static/* gs://demo-assets
```

At this point you should be able to load up the website by visiting
the external IP for the app service (obtained by running `kubectl get
svc`) in your browser.

Go to `http://app-service-external-ip/admin/` to login using the
credentials you setup earlier (while creating a super user), and
return to the site to add some blog posts. Notice that as you refresh
the site, the name of the app pod serving the site changes, while the
content stays the same.

### Play around to get a feeling for Kubernetes’ API

Now, suppose your site isn’t getting much traffic, you can gracefully
_scale_ down the number of running application pods to one. (Similarly
you can increase the number of pods if your traffic starts to grow!)
```sh
kubectl scale rc app-orange --replicas=1
kubectl get pods
```

You can check _resiliency_ by deleting one or more app pods and see it
respawn.
```sh
kubectl delete pod <pod-id>
kubectl get pods
```

Notice Kubernetes will spin up the appropriate number of pods to match
the last known state of the replication controller.

Finally, to show how we can migrate from one version of the site to
the next, we’ll move from the existing orange version of the
application to another version that’s maroon.

First we scale down the orange version to just one copy:
```sh
kubectl scale rc app-orange --replicas=1
kubectl get pods
```

Then we spin up some copies of the new maroon version:
```sh
cd kubernetes/app
kubectl create -f replication-controller-maroon.yaml
kubectl get pods
```

Notice that because the app service is pointing simply to the label
`name: app`, both the one orange and the three maroon apps respond to
http requests to the external IP.

When you’re happy that the maroon version is working, you can spin
down all remaining orange versions, and delete its replication
controller.
```sh
kubectl scale rc app-orange --replicas=0
kubectl delete rc app-orange
```

### 清理

After you’re done playing around with this example, remember to
cleanly discard the compute resources we spun up for it.
```sh
gcloud container clusters delete demo
gsutil -m rm -r gs://demo-assets
```

## 总结

This article covered a lot of ground. We first motivated the need for
containers and cluster orchestration frameworks in general. We then
saw how Docker and Kubernetes in particular help us deploy a Django
application that can scale gracefully to meet loads, while
simultaneously being resilient to arbitrary failures of underlying
compute resources.

While this is a good introduction to concepts, there are a few details
I glossed over which you will want to consider carefully before
deciding if Kubernetes is right for you.

The first is that the setup of a Kubernetes cluster (when not using a
hosted version like Google Container Engine, as in our example) is
non-trivial. And while Kubernetes attempts to abstract away the
underlying hardware, the actual experience you have using it is quite
dependent on the actual infrastructure you’re running on. So do play
around with it in your environment to gauge if the complexity is worth
it for you.

The second is that our example deployment needs more work using
additional Kubernetes primitives before it becomes useful in
practice. These include using:

*   _Persistent Volumes_ (and _Persistent Volume Claims_) to ensure that
the PostgreSQL data is persistent beyond the life of its pod.
*   _Secrets_ to handle the database password and other sensitive
information.
*   _Horizontal Pod Autoscaling_ to automatically adjust the number of
running pods based on observed CPU utilisation.
*   _Daemon Sets_ to help aggregate logging across nodes.

Keep an eye on [the issues list for the example project](https://github.com/hnarayanan/kubernetes-django/issues) to
find out more about progress on these fronts. And you’re free to help
out too. You can also add additional pieces to the puzzle (such as
Redis or Elasticsearch). Pull-requests are more than welcome if
you work any of these out!

I’ll leave you with the one thought that really excites me about all
this. There is fascinating philosophical shift going on right now
where we’re turning our attention from _managing servers_ to simply
_running components of our app_. And this level of abstraction feels
just right.

## 选定的参考和进一步阅读

1.  [Linux Containers: Parallels, LXC, OpenVZ, Docker and
More](http://aucouranton.com/2014/06/13/linux-containers-parallels-lxc-openvz-docker-and-more/)
2.  [Borg, Omega, and Kubernetes](http://queue.acm.org/detail.cfm?id=2898444)
3.  [Building Scalable and Resilient Web Applications on Google Cloud
Platform](https://cloud.google.com/solutions/scalable-and-resilient-apps)
4.  Understanding Kubernetes from the ground up — [Kubelet](http://kamalmarhubi.com/blog/2015/08/27/what-even-is-a-kubelet/),
[API Server](http://kamalmarhubi.com/blog/2015/09/06/kubernetes-from-the-ground-up-the-api-server/), [Scheduler](http://kamalmarhubi.com/blog/2015/11/17/kubernetes-from-the-ground-up-the-scheduler/)
5.  [Packaging Django into containers](http://michal.karzynski.pl/blog/2015/04/19/packaging-django-applications-as-docker-container-images/)
6.  [Running Postgres Inside Kubernetes With Google Container Engine](https://blog.oestrich.org/2015/08/running-postgres-inside-kubernetes/)
7.  Deploying Django with Kubernetes — [Talk](https://www.youtube.com/watch?v=HKKUgWuIZro),
[Example Code](https://github.com/waprin/kubernetes_django_postgres_redis)
8.  Deploying a containerised Rails app to Google Container Engine with
Kubernetes — [Part 1](http://www.thagomizer.com/blog/2015/05/12/basic-docker-rails-app.html), [Part
2](http://www.thagomizer.com/blog/2015/07/01/kubernetes-and-deploying-to-google-container-engine.html), [Part 3](http://www.thagomizer.com/blog/2015/08/18/k8s_secrets.html)