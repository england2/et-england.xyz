---
title: Home
---

# Elan Thomas England

This is a portfolio website that discusses some projects I've built.

Scroll down to read an introduction to each project, and click the heading to see the full write-up.

    
## [Shellbin](/shellbin/)
<hr>

Shellbin is a microservice architecture project that I built to exercise my understanding of CI/CD for cloud-native applications.

It's named shellbin because it's a pastebin clone that you can access with your shell using Unix pipes and the `netcat` utility.

```fish
cat $FILE | nc sb.cat-z.xyz
```

Users can also create pastes with a web front-end written in Go (which uses Server Side Rendering).

<br>

In total, there are 4 distinct container images involved in this project: A web server, a database service, a netcat-receiving-server, and the MySQL database container.

As mentioned, the main goal of this project was to implement a developement pipeline for these microservices.

The pipeline ends up being pretty simple.

- First, shellbin's Kubernetes manifests are sourced from a Helm chart that is tracked by ArgoCD
  - ArgoCD makes sure that the most recent version's of the applications manifests are deployed
- When we push to the shellbin repo, our GitHub Actions workflow goes something like this:
  - builds the container images, then pushes them to GitHub Container Registry (GHCR)
  - clones the Kubernetes cluster's declarative gitop repository
  - modifies the image tags in the Helm Chart's values.yml so they point to the newly pushed images
  - commits and pushes the diff that has the new image tags to cluster's configuration repo
- And then ArgoCD picks up changes and the cluster deploys any updates to the container images and Helm Chart

Read the [full Shellbin write-up here](/shellbin/) for more details.

<br>
    
## [Webterm](/webterm/)
---

Webterm is a system that allows users to access Unix machines from their web browser.

Of note is that there's a binary that allocates and exposes backend terminal instances automatically.

Here's a horrific piece of code which is what happens when you to try interact with the raw Kubernetes API instead of just using [Kubebuilder](https://github.com/kubernetes-sigs/kubebuilder).

```go
	for {
		select {
		case paramToAppend := <-fil.paramStream:
			fil.params = append(fil.params, &paramToAppend)
			fmt.Printf("params: %v\n", fil.params)
		case indexToRemove := <-fil.remIndexChan:
			fil.params = remove(fil.params, indexToRemove)
		case event := <-fil.inChan:
			for _, fp := range fil.params {
				if fp.pass(event, fil.done) {
					fp.outChan <- event
				}
			}
		case <-fil.done:
			return
		default:
			if len(fil.params) == 0 {
				close(fil.done)
				runningFilter = nil
			}
		}
	}
```

Terrible! And kind of cool.

Here are some of the moving-pieces involved with this project:
- Website frontend that emulates a terminal, and client-side JS to request a new Unix machine container from the Kubernetes cluster.
- Containers to run the Unix machine that is served to clients.
- Synchronization between pseudo-terminal hosting containers and website frontend

Read the [full Webterm write-up here](/shellbin/) for more details.
