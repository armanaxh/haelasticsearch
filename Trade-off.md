The main difference is that the Helm Charts are pretty unopinionated while the Operator is opinionated — 
it has a lot of best practices built-in like a hard requirement on using security. Also, the Operator Framework is built on the reconciliation loop and will continuously check if your cluster is in the desired state or not. Helm Charts are more like a package manager where you run specific commands. And, In ECS Operator you have an admission controller.

I don't have experience in Elasticsearch before, But In this project, I really liked the ECS operator.

Another hand, I think if you want to use the elastic stack for some juggling way. use should deploy with raw manifest(writing k8s core objects). For example when you want to change the JVM config in a very spasific way. Or some sidecar or etc.

Another difference is that the Helm Charts are open source while the Operator is free, but uses the Elastic License (you can't use it to run a paid Elasticsearch service is the main limitation).

I preferred Operator, But I believe I can deploy elastic stack in the raw manifest and Helm too.

Thanks,
Arman Absalan
armanaxh@gmail.com