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

  ![Layout of a non-trivial Django application.](https://harishnarayanan.org/images/writing/kubernetes-django/standard-django-application.svg)
  <figcaption>Layout of a non-trivial Django application.</figcaption>

当你第一次开始了你的应用，并且只有少量用户时，它可以完美的将所有东东都运行在一台服务器上。所以你把应用跑在你[最喜欢的云服务提供商](https://m.do.co/c/e3559ea013de)在，启动一个VPS来运行Debian或其他什么操作系统，并在同一台机器上安装所有这些软件。


  ![All pieces making up the app on a single machine.](https://harishnarayanan.org/images/writing/kubernetes-django/all-in-one-server.svg)
  <figcaption>All pieces making up the app on a single machine.</figcaption>

然后，随着你的应用开始变得受欢迎，你开始进行扩展工作。搜寻，你遵循简单的方法，简单的提供越来越大的单一机器来运行你应用。这就是所谓的_垂直扩展(vertical scaling)_，它行之有效，知道应用拥有了上千个用户。

接着，你的应用变得更受欢迎。

现在，你意识到，如果你分开组成你应用的组件，然后将它们放在不同的机器上，那么你就可以独立地扩展组件。这意味着，例如，你可以运行Django应用的多个实例（称为_水平扩展(horizontal scaling)_）来处理不断增长的用户群，同时继续把你的PostgreSQL服务器运行在唯一一个（但可能日益强大的）机器上。


  ![Running many instances of the app, talking to a single database.](https://harishnarayanan.org/images/writing/kubernetes-django/on-separate-servers.svg)
  <figcaption>Running many instances of the app, talking to a single database.</figcaption>

其实，这是一个相当不错的部署方案(并且它的基本理念是我们今天在我的[日常工作](https://edgefolio.com/company/)中实践的基础，使用[Ansible](https://www.ansible.com)来设置服务器)，但它还有一些不便之处：

1.  为每个组件建立并保持最新的服务器是烦人的。这不是你想考虑的关于服务器的问题。

2.  通常，你拥有较差的资源利用率，因为每一个组件都不能有效地使用它所运行在的服务必须提供的所有资源。这主要是因为你通常为高峰负载进行安装，而不是平均负载。

3.  如果你试图通过在同个机器上运行多个组件（例如，应用和数据库）来解决(2)，那么就没有办法阻止组件之间的资源抢占。例如，在一个给定服务器上的匮乏资源隔离。

* * *

所以，如果我们能够将我们的关注点从管理服务器转移到简单在计算资源的集合上运行我们的应用的组件呢？此外，如果这些组件之间很好的相互隔离，并且有效地利用它们所拥有的资源呢？

然后，我们的部署图可能看起来更像下面这样，其中，我们关心的主要组件（应用组件）以橙色显示。组件运行的实际节点（物理机或虚拟机）在视觉上弱化了，因为我们不关心细节。并且，我们相信我们的基本计算基础架构为我们提供了一些基本功能，例如持久性存储和负载平衡器（以绿色显示）这些任何较重的web应用的常用功能。


  ![The application running on an abstract collection of resources.](https://harishnarayanan.org/images/writing/kubernetes-django/scheduled-on-cluster.svg)
  <figcaption>The application running on an abstract collection of resources.</figcaption>

这种理念的转变 — [从 _管理服务器_ 到简单理想地 _运行我们应用的组件_](http://queue.acm.org/detail.cfm?id=2898444) — 恰恰是容器技术，例如[Docker](https://www.docker.com/)，和集群业务流程框架，例如[Kubernetes](http://kubernetes.io/)，所要提供的。而在实际的[下面的例子](#practical-example-on-google-container-engine:d4c663f7b7e5088d61b55e9f2c9602ed)中，我们将看到这些工具如何让我们能够轻松地重新创建上面显示的理想部署方案。

## 所以，Docker和Kubernetes是如何行之有效的呢？

你可以通俗地把[Docker](https://www.docker.com/)容器当做你的应用的[fat静态二进制文件](https://en.wikipedia.org/wiki/Static_library)。它们捆绑你的应用代码，底层库，以及你的应用程序需要运行的所有东东到一个便捷的包里，这个包可以直接的在Linux内核上的一个薄层之上运行。这在实践中意味着，你可以获得一个你已经构建了一次的容器，然后把它运行在不同版本的Linux发行版本上，或者完全不同的Linux发行版本上。所有这一切都应该无缝工作。

因此，通过形成一个可构建、测试已经随处运行的基本单元，容器将你的关注层次提高到操作系统的细节上，运行你关注自己的应用。容器还提供资源隔离，意味着如果其中两个并排运行，那么每一个都只能看到它应该看到的东东，以及做它应该做的事。

这意味着我们的部署之旅现在可以大略分成两个步骤。第一个步骤是得到我们的应用的不同组件，然后将它们打包到容器中。第二步是在我们的计算资源之上运行它们 —— 利用底层计算原语，例如_负载均衡器_，并确保容器正确联网。

这里，第二步就是[Kubernetes](http://kubernetes.io/docs/whatisk8s/)的用武之地。

Kubernetes是一个用于管理集群和部署“容器化”应用的开源系统。Kubernetes抽象（你的云供应商或者本地集群的）底层硬件，并提供一个简单的API以允许你容易地控制它。你向这个API发送一些声明式状态，例如：“我想要我的Django应用容器的三个副本运行在负载均衡器之后，拜托了”，然后，它可以确保在你的集群的节点上安排适当的容器。另外，它监控状态，并确保维持此状态，允许其对系统中的任意修改保持健壮。例如，这意味着，如果一个容器因为节点内存耗尽而被过早关闭，Kubernetes会注意到这点，并确保重新启动其他位置的另一个副本。

Kubernetes通过位于集群的每个节点上的代理来工作。这些代理允许一些行为，例如运行Docker容器 (_docker守护进程_)，确保维持需要的状态 (_kubelet_)，以及容器可以彼此交流 (_kube-proxy_)。这些代理监听及与一个集中的API服务器同步，以确保系统处于期望的状态。


  ![A simplified look at Kubernetes](https://harishnarayanan.org/images/writing/kubernetes-django/kubernetes-architecture.svg)
  <figcaption>A simplified look at Kubernetes' architecture.</figcaption>

Kubernetes API暴露了集群配置资源集合，我们可以修改以表达我们希望我们的集群所处的状态。该API提供给了一个标准的REST接口，允许我们以以多种方式与它进行交互。在即将到来的例子中，我们将使用一个瘦命令行客户端，名为`kubectl`，来与API服务器进行通信。

虽然该API[提供大量原语](http://kubernetes.io/kubernetes/third_party/swagger-ui/)以供使用，但是这里还是有一些对于我们今天的例子重要的东西：

*   **Pod**是在相同节点上被安排在一起的紧密耦合的容器的集合，允许它们共享卷和本地网络。它们是可以部署在一个Kubernetes集合的最小单元。

*   **标签(Label)**是任意的与Kubernetes资源相关的键/值对 (例如，`name: app`或者`stage: production`)。它们允许以一种简单的方式来选择和组织资源组。

*   **复制控制器(Replication Controller)**确保指定数量的（特定类型的）pod在任何给定时间运行。它们通过标签来对pod进行分组。

*   **服务(Service)**提供了一组执行相同功能的pod的逻辑分组。通过为这组pod提供一个永久名称，IP地址或者端口，它们提供服务发现和负载均衡的功能。

如果此时，这些都看起来有点太抽象，请不要烦恼。现在，我们要跳到一个例子，这个例子证明了这些在实践中如何工作，以助我们部署我们的Django应用。

## 谷歌容器引擎（Google Container Engine）上的应用实例

我们将要关注的例子应用是一个简单的博客应用。


  ![Sample blog app following the Django Girls Tutorial.](https://harishnarayanan.org/images/writing/kubernetes-django/django-girls-blog-screenshot.png)

虽然这是一个非常简单的例子，但是它包含了我们在实践中讨论的想法所有需要看到的必须组件。在这个例子的过程中，我们会访问由Kubernetes控制的集群，分开我们的博客应用到不同的Docker容器中，并使用Kubernetes进行部署。最终结果与前面介绍的理想化图相匹配。


  ![The application running on an abstract collection of resources.](https://harishnarayanan.org/images/writing/kubernetes-django/scheduled-on-cluster.svg)

一旦我们让事情运转起来，我们会使用Kubernetes API来做不同的事情，如缩放应用，观察它如何从失败中恢复，并学习如何在无需停机的情况下将你的Django应用从一个版本升级到另一个版本。

### 预备步骤

1.  获取此示例的代码。
```
    git clone https://github.com/hnarayanan/kubernetes-django.git
```
2.  [安装Docker](https://docs.docker.com/engine/installation/)。
3.  看看并感受一下在这个仓库中使用的[例子Django应用](https://github.com/hnarayanan/kubernetes-django/tree/master/containers/app)。这是一个简单的博客，它的构建遵循优秀的[Django Girls教程](http://tutorial.djangogirls.org)。
4.  [安装一个由Kubernetes管理的集群](http://kubernetes.io/docs/getting-started-guides/)。要做到这一点可能需要付出巨大的努力，所以一个简单的入门方法是早谷歌云平台（免费）注册，并使用一个名为[谷歌容器引擎(Google Container Engine)](https://cloud.google.com/container-engine/)(GKE)的Kubernetes托管版本。

    1.  在谷歌云平台上建立一个账户，然后更新你的账单资料。
    2.  安装[命令行接口](https://cloud.google.com/sdk/).
    3.  创建一个使用该web接口的项目（今后我们将用`$GCP_PROJECT`指代）。
    4.  现在，我们我们已经准备好了设置一些基本配置。
    ```
    gcloud config set project $GCP_PROJECT
    gcloud config set compute/zone europe-west1-d
    ```
    5.  然后创建集群自身。
    ```
    gcloud container clusters create demo
    gcloud container clusters list
    ```
    6.  最后，配置`kubectl`来与该集群通信。
    ```
    gcloud container clusters get-credentials demo
    kubectl get nodes
    ```

### 创建及发布Docker容器

在这个例子中，我们将使用[Docker Hub](https://hub.docker.com/)来集群我们的容器。而且，由于我们并没有什么敏感信息，因此我们将公开这些容器。

#### PostgreSQL

构建容器，记得用你自己在Docker Hub上的用户名来取代`hnarayanan`:
```sh
cd containers/database
docker build -t hnarayanan/postgresql:9.5 .
```

如果你想要的话，可以将它迁出到本地：
```sh
docker run --name database -e POSTGRES_DB=app_db -e POSTGRES_PASSWORD=app_db_pw -e POSTGRES_USER=app_db_user -d hnarayanan/postgresql:9.5
# Echoes $PROCESS_ID to the screen
docker exec -i -t $PROCESS_ID bash
```

将它推送到一个仓库中：
```sh
docker login
docker push hnarayanan/postgresql:9.5
```
#### 在Gunicorn中运行Django app

构建容器：
```sh
cd containers/app
docker build -t hnarayanan/djangogirls-app:1.2-orange .
```

将它推送到一个仓库中：

` docker push hnarayanan/djangogirls-app:1.2-orange`

稍后，在这个例子中，我们将看到如何执行滚动更新。要做到这点，让我们创建我们的应用的另一种版本，这个版本只有不同的标题颜色，然后构建一个新的容器应用，接着也把它推送到该容器仓库。
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

虽然我们的应用只需要运行一个PostgreSQL实例，但是我们仍然将其运行在一个(pod)复制控制器下。通过这种方式，我们拥有了一个服务，这个服务监控我们的数据库pod，确保即使一些奇怪的情况发生，例如底层的节点出现故障，我们的实例也是运行的。

```sh
cd  kubernetes/database
kubectl create -f replication-controller.yaml

kubectl get rc
kubectl get pods

kubectl describe pod <pod-id>
kubectl logs <pod-id>
```

现在，我们启动一个服务来指向该pod。
```sh
cd  kubernetes/database
kubectl create -f service.yaml

kubectl get svc
kubectl describe svc database
```

#### 在Gunicorn中运行Django应用

首先，我们有了与单一数据库通信的三个应用pod（橙色应用容器的副本）。
```sh
cd kubernetes/app
kubectl create -f replication-controller-orange.yaml
kubectl get pods

kubectl describe pod <pod-id>
kubectl logs <pod-id>
```

然后，我们启动了一个服务指向该pod。这是一个带有外部IP的负载均衡器，所以我们可以访问该站点。
```sh
cd kubernetes/app
kubectl create -f service.yaml
kubectl get svc
```

在我们使用`kubectl get svc`所显示的外部IP来访问该网站之前，我们需要做几件事：

1.  执行初始迁移：`kubectl exec <some-app-orange-pod-id> -- python /app/manage.py migrate` 

2.  为该博客创建一个初始用户：`kubectl exec -it <some-app-orange-pod-id> -- python /app/manage.py createsuperuser`

3.  拥有一个CDN主机静态文件，因为我们不想要使用Gunicorn。该演示使用谷歌云存储，但是你可以自由使用任何你想要的存储。只要确保在`containers/app/mysite/settings.py`中的`STATIC_URL`参数反映了文件所在位置即可。

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


此时，你应该可以通过访问用于该应用服务的外部IP（运行`kubectl get svc`获得）在你的浏览器中加载该网站。

使用你前面（在创建一个超级用户时）安装的凭证登录`http://app-service-external-ip/admin/`，并返回到该网站，创建一些博文。注意，当你刷新该网站时，为该站点服务的应用pod的名称发生了改变，而内容保持不变。

### 感受一下Kubernetes API吧

现在，假设你的网站并没有获得多少流量，你可以优雅地降低运行的应用pod的数量到1.（类似地，如果你的流量开始增长，那么你可以增加pod的数量！）
```sh
kubectl scale rc app-orange --replicas=1
kubectl get pods
```

你可以通过删除一个或多个应用pod来检查其灵活性，并看到它重生（respawn）。
```sh
kubectl delete pod <pod-id>
kubectl get pods
```

注意，Kubernetes会调整pod的数量以匹配该复制控制器最后的已知状态。

最后，要显示我们可以怎样迁移该站点的一个版本到下一个版本，我们将从应用现有的橙色版本移动到另一个栗色版本。

首先，将我们的橙色版本缩小到只有一个副本：
```sh
kubectl scale rc app-orange --replicas=1
kubectl get pods
```

接着，我们启动该新的栗色版本的一些副本：
```sh
cd kubernetes/app
kubectl create -f replication-controller-maroon.yaml
kubectl get pods
```

注意，由于该应用简单地指向标签`name: app`，因此这个橙色版本的应用和三个栗色版本的应用都响应http请求道外部IP。

当你愉快地看到该栗色版本工作了，那么你就可以关闭剩下的橙色版本，并删除它的复制控制器了。
```sh
kubectl scale rc app-orange --replicas=0
kubectl delete rc app-orange
```

### 清理

在你结束了这个例子后，记得干净地弃用我们为它启动的计算资源。
```sh
gcloud container clusters delete demo
gsutil -m rm -r gs://demo-assets
```

## 总结

本文介绍了许多基础知识。我们首先激发了在一般情况下使用容器和集群编配框架的需要的积极性。然后，我们看到了Docker和Kubernetes如何帮助我们部署一个Django应用，这个应用可以优雅地扩展以满足负载，同时对于任意的底层计算资源故障都具有弹性。

虽然这是对于概念的一个很好的介绍，但我掩盖了一些在你决定Kubernetes是否适合你之前会想要仔细考虑的细节。

首先，Kubernetes集群的安装（当不使用托管版本，例如在我们的例子中的谷歌容器引擎）一点都不简单。虽然Kubernetes试图抽象体层硬件，但是你使用它的真实体验非常依赖于你正在运行的实际基础设施。所以，务必在你的环境上玩一玩它，以衡量你是否能够接受它的复杂度。


其次是，在它变得实际有用之前，我们的示例部署需要使用额外的Kubernetes原语来进行更多的工作：

*   _持久卷(Persistent Volume)_ (和 _Persistent Volume Claim_)确保PostgreSQL数据超越Pod生命周期持久存在。
*   _Secret_ 处理数据库密码和其他敏感信息。
*   _水平Pod自动缩放(Horizontal Pod Autoscaling)_基于所观察到的CPU使用率自动调整运行的Pod的数量。
*   _守护进程(Daemon Set)_帮助汇总跨节点日志。

留心[示例项目的问题列表](https://github.com/hnarayanan/kubernetes-django/issues) ，以了解更多有关这些方面的进展。而且你也可以自由地为此出一份力。你也可以添加额外的组件到里面（例如Redis或者Elasticsearch）。如果你实现了这些，非常欢迎拉取请求！


我将留给你一个真的令我非常兴奋的想法。神奇的理念转变正在发生，我们正将我们的注意力从管理服务器简单转变成运行我们的应用的组件。而这个级别的抽象感觉恰到好处。

## 选定的参考和进一步阅读

1.  [Linux容器: Parallels, LXC, OpenVZ, Docker等等](http://aucouranton.com/2014/06/13/linux-containers-parallels-lxc-openvz-docker-and-more/)
2.  [Borg, Omega, 和Kubernetes](http://queue.acm.org/detail.cfm?id=2898444)
3.  [在谷歌云平台上构建可扩展和弹性的Web应用](https://cloud.google.com/solutions/scalable-and-resilient-apps)
4.  从头开始了解Kubernetes — [Kubelet](http://kamalmarhubi.com/blog/2015/08/27/what-even-is-a-kubelet/), [API Server](http://kamalmarhubi.com/blog/2015/09/06/kubernetes-from-the-ground-up-the-api-server/), [Scheduler](http://kamalmarhubi.com/blog/2015/11/17/kubernetes-from-the-ground-up-the-scheduler/)
5.  [把Django打包到容器中](http://michal.karzynski.pl/blog/2015/04/19/packaging-django-applications-as-docker-container-images/)
6.  [使用谷歌容器引擎在Kubernetes中运行Postgres](https://blog.oestrich.org/2015/08/running-postgres-inside-kubernetes/)
7.  使用Kubernetes部署Django — [Talk](https://www.youtube.com/watch?v=HKKUgWuIZro), [示例代码](https://github.com/waprin/kubernetes_django_postgres_redis)
8.  使用Kubernetes部署一个容器化的Rails应用到谷歌容器引擎 — [第一部分](http://www.thagomizer.com/blog/2015/05/12/basic-docker-rails-app.html), [第二部分](http://www.thagomizer.com/blog/2015/07/01/kubernetes-and-deploying-to-google-container-engine.html), [第三部分](http://www.thagomizer.com/blog/2015/08/18/k8s_secrets.html)