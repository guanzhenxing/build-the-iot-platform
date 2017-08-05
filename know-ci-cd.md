# 如何理解持续集成、持续交付、持续部署 #

在我们开发软件的时候，我们经常听到持续集成、持续交付和持续部署等词语。这些词是什么意思呢？有什么区别？

我们先来看个图：

<img src="https://raw.githubusercontent.com/guanzhenxing/build-the-iot-platform/master/resources/dev-phase.png">

在图中，我们把整个软件开发分为：编码、构建、集成、测试、交付和部署这几个阶段。而持续集成、持续交付和持续部署对应的是整个过程的不同的阶段。

## 持续集成 ##

持续集成（Continuous Integration）指的是开发人员频繁地（一天多次）先主干提交代码，然后能够立刻自动进行构建、单元测试、集成测试，以便能更快地发现代码中的错误。“持续集成”源自于极限编程（XP），是 XP 最初的 12 种实践之一。

<img src="https://raw.githubusercontent.com/guanzhenxing/build-the-iot-platform/master/resources/ci-flow.png">（[图片来源](http://www.mindtheproduct.com/2016/02/what-the-hell-are-ci-cd-and-devops-a-cheatsheet-for-the-rest-of-us/)）

如上图所示：当开发人员提交代码以后，CI服务器自动从代码库中拉取代码，自动进行编译、测试（单元测试，集成测试），并将结果反馈给开发人员。

这样的好处有：

- 开发人员能够快速发现错误；将错误消除在集成到主干之前；
- 将重复性的手工流程自动化，让工程师更加专注于代码；
- 能够快速生成可部署的软件，提高软件的成熟度；
- 增强开发人员对产品的信心，帮助建立更好的工程师文化；

## 持续交付 ##

持续交付（Continuous Delivery）在持续集成的基础上，将集成后的代码部署到更贴近真实运行环境的「类生产环境」（production-like environments）中。是频繁地将软件的新版本，交付给质量团队或者用户，以供评审。如果评审通过，代码就可以手动部署到生产环境。它强调的是，不管怎么更新，软件是随时随地可以交付的。

<img src="https://raw.githubusercontent.com/guanzhenxing/build-the-iot-platform/master/resources/cd-m-flow.png">（[图片来源](http://www.mindtheproduct.com/2016/02/what-the-hell-are-ci-cd-and-devops-a-cheatsheet-for-the-rest-of-us/)）

上图的TEST和STAGING可以理解为在模拟环境（预生产环境）进行更多的测试。

持续交付的好处：

- 快速发布。能够应对业务需求，同时获得迅速反馈，并更快地实现软件价值；
- 高质量的软件发布标准。整个交付过程标准化、可重复、可靠；
- 更先进的团队协作方式。从需求分析、产品的用户体验到交互设计、开发、测试、运维等角色密切协作，相比于传统的瀑布式软件团队，更少浪费；

## 持续部署 ##

持续部署（Continuous Deployment）是持续交付的下一步，指的是代码通过评审以后，自动部署到生产环境。这意味着，所有通过了一系列的自动化测试的改动都将自动部署到生产环境。它也可以被称为“Continuous Release”。持续部署的目标是，代码在任何时刻都是可部署的，可以进入生产阶段。

<img src="https://raw.githubusercontent.com/guanzhenxing/build-the-iot-platform/master/resources/cd-a-flow.png">（[图片来源](http://www.mindtheproduct.com/2016/02/what-the-hell-are-ci-cd-and-devops-a-cheatsheet-for-the-rest-of-us/)）

持续交付和持续部署的差别就是手动或者自动部署到生产环境。

持续部署的好处：

- 可以相对独立地部署新的功能，并能快速地收集真实用户的反馈；
- You build it, you run it；
- 可以早点下班...

---
参考：

- [谈谈持续集成，持续交付，持续部署之间的区别](http://blog.flow.ci/cicd_difference/)
- [如何理解持续集成、持续交付、持续部署？](https://www.zhihu.com/question/23444990)
- [持续集成是什么？](http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html)

可以仔细读一读的资料：

- [CONTINUOUS INTEGRATION](https://www.thoughtworks.com/continuous-integration)
- [The Product Managers’ Guide to Continuous Delivery and DevOps](http://www.mindtheproduct.com/2016/02/what-the-hell-are-ci-cd-and-devops-a-cheatsheet-for-the-rest-of-us/)